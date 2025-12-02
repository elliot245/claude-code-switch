# Fix: DISABLE_ORACLE=true Not Working

## Issue
The `DISABLE_ORACLE=true` environment variable in `order_example.go` was not properly disabling oracle integration. The example code attempted to modify the Oracle configuration after calling `cfg.GetNetworkConfig()`, but this was modifying a local copy instead of the actual configuration used by the client.

## Root Cause
```go
// ❌ BEFORE: This doesn't work
if os.Getenv("DISABLE_ORACLE") == "true" {
    netCfg := cfg.GetNetworkConfig()  // Returns a COPY
    netCfg.Oracle.Enabled = false      // Modifies the copy, not the original
}
```

The problem:
1. `GetNetworkConfig()` returns a **value copy** (not a pointer)
2. Modifying `netCfg.Oracle.Enabled` only affects the local copy
3. When `NewPerpClient(cfg)` is called, it calls `GetNetworkConfig()` again, getting a fresh copy with default settings
4. The OracleInterceptor receives the fresh config, so `IsOracleEnabled()` always returns `true`

## Solution
Added Oracle configuration override support to the Config struct, following the existing pattern for RPCURL and GRPCEndpoint overrides.

### Changes Made

#### 1. Added Oracle Field to Config Struct (`config/config.go`)
```go
type Config struct {
    Network Network

    // Optional overrides
    RPCURL       string
    GRPCEndpoint string

    // NEW: Oracle configuration override
    Oracle *OracleConfig // Override oracle configuration

    EventBackend EventBackend
}
```

#### 2. Updated GetNetworkConfig() to Merge Oracle Config (`config/config.go`)
```go
func (c *Config) GetNetworkConfig() NetworkConfig {
    var base NetworkConfig
    // ... load base config ...

    // Apply user overrides
    if c.RPCURL != "" {
        base.RPCURL = c.RPCURL
    }
    if c.GRPCEndpoint != "" {
        base.GRPCEndpoint = c.GRPCEndpoint
    }
    // NEW: Merge oracle configuration override
    if c.Oracle != nil {
        base.Oracle = *c.Oracle
    }

    return base
}
```

#### 3. Fixed Example Code (`examples/order/order_example.go`)
```go
// ✅ AFTER: This works correctly
if os.Getenv("DISABLE_ORACLE") == "true" {
    cfg.Oracle = &config.OracleConfig{Enabled: false}
}
```

## Verification

### Tests Added
1. **config/config_oracle_test.go** - Tests Oracle configuration override functionality
   - Default testnet config (oracle enabled)
   - Testnet with oracle disabled
   - Testnet with oracle explicitly enabled
   - Custom provider configuration
   - Config isolation (verify copies don't affect base)

2. **examples/order/order_example_test.go** - Tests environment variable behavior
   - Oracle enabled by default
   - Oracle disabled via env var
   - Oracle enabled with false env var

3. **oracle/interceptor_test.go** - Tests OracleInterceptor behavior
   - Disabled config (no oracle updates)
   - Enabled config without feed IDs (error)
   - Operations that don't need oracle (liquidation)

### Test Results
```
✅ All config tests pass
✅ All example tests pass
✅ All oracle interceptor tests pass
✅ Full test suite passes (go test ./...)
✅ No vet issues (go vet ./...)
✅ Build successful (go build ./...)
```

## How It Works Now

### With DISABLE_ORACLE=true
```go
cfg := &config.Config{
    Network: config.Testnet,
    Oracle:  &config.OracleConfig{Enabled: false},
}

// OracleInterceptor.PreBuild() checks IsOracleEnabled()
// Returns early without adding oracle commands
// PlaceOrder proceeds without oracle price updates
```

### With DISABLE_ORACLE=false (or unset)
```go
cfg := &config.Config{
    Network: config.Testnet,
    // Oracle not set, uses TestnetConfig defaults (Enabled: true)
}

// OracleInterceptor.PreBuild() checks IsOracleEnabled()
// Adds oracle update commands to transaction
// PlaceOrder uses fresh oracle prices
```

## Architectural Benefits
1. **Consistent Pattern**: Follows existing RPCURL/GRPCEndpoint override pattern
2. **Flexible**: Allows full oracle configuration override, not just Enabled flag
3. **Clean Separation**: Config struct controls settings, GetNetworkConfig() merges them
4. **Testable**: Easy to test with different configurations
5. **Future-Proof**: Can override other oracle settings (provider, cache TTL, etc.)

## Files Modified
- `sdks/golang/perp/config/config.go` - Added Oracle field and merge logic
- `sdks/golang/perp/examples/order/order_example.go` - Fixed example code

## Files Created
- `sdks/golang/perp/config/config_oracle_test.go` - Oracle config override tests
- `sdks/golang/perp/examples/order/order_example_test.go` - Example environment var tests
- `sdks/golang/perp/oracle/interceptor_test.go` - OracleInterceptor behavior tests

## Impact
- ✅ No breaking changes
- ✅ Backward compatible (Oracle field is optional)
- ✅ No performance impact
- ✅ Consistent with existing patterns
- ✅ Well-tested with comprehensive coverage
