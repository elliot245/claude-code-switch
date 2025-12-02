# Technology Context: Technical Foundation

## Core Technology Stack

**Primary Technology:** Pure Bash Scripting (1,713 lines in ccm.sh)
**Rationale:** Universal availability, shell integration, zero dependencies
**Alternatives Considered:** Python (dependency overhead), Node.js (ecosystem mismatch), Go (complexity)

**Platform Support:** macOS, Linux (Unix-based systems)
**Deployment:** Direct script execution + shell function installation
**Integration:** Claude Code IDE via environment variable injection

## Technology Constraints

### 1. Shell Compatibility Requirements
**Constraint:** Must work across bash 3.2+ (macOS default) to bash 5.x (modern Linux)
**Impact:** Limited to POSIX-compatible features, avoid bash-specific extensions
**Workaround:** Feature detection with conditional implementation

### 2. No External Dependencies
**Constraint:** Zero additional package installation requirements
**Impact:** Must use only standard Unix tools (curl, grep, sed, awk)
**Benefits:** Universal compatibility, easy installation, security compliance

### 3. Claude Code Integration Limitations
**Constraint:** Must integrate through environment variables, no direct API access
**Impact:** Dependent on IDE's environment variable support, potential auth conflicts
**Mitigation:** Comprehensive testing across Claude Code versions

## API Integration Architecture

### 1. Multi-Provider API Support
**Providers Supported:** Anthropic Claude, DeepSeek, KIMI, GLM, Qwen, LongCat
**Integration Pattern:** HTTP requests with provider-specific authentication headers
**Standardization:** All providers expose Anthropic-compatible API endpoints

### 2. PPINFRA Fallback Service
**Integration:** DeepSeek experience key + PPINFRA routing infrastructure
**Reliability:** Automatic failover from official APIs to backup service
**Performance:** Timeout management (10-minute default) with service switching

### 3. Authentication Management
**Pattern:** API key storage with optional macOS Keychain integration
**Security:** Token masking in output, secure file permissions
**Validation:** Format checking and service availability verification

## Development Environment Setup

### 1. Local Development
**Requirements:** Bash shell, standard Unix tools, git for version control
**Testing:** Manual testing across multiple provider configurations
**Debugging:** `bash -x` execution tracing, detailed error logging

### 2. Cross-Platform Considerations
**macOS Specific:** Keychain integration, default bash 3.2 compatibility
**Linux Specific:** Distribution differences, shell environment variations
**Testing Strategy:** Virtual machine testing across multiple distributions

### 3. IDE Integration Development
**Claude Code Specific:** Environment variable injection timing, auth conflict resolution
**Testing Approach:** Multiple Claude Code versions, configuration scenarios
**Limitations:** Dependent on Claude Code's environment variable support consistency

## Performance Considerations

### 1. Startup Performance
**Critical Path:** Script loading, configuration parsing, environment setup
**Optimization:** Lazy loading of translations, efficient configuration validation
**Target:** Sub-second startup time for interactive use

### 2. Network Performance
**Timeout Management:** Configurable timeouts (default 10 minutes)
**Retry Logic:** Exponential backoff for network failures
**Fallback Switching:** Automatic provider switching on performance degradation

### 3. Memory Usage
**Constraint:** Minimal memory footprint for script execution
**Monitoring:** Process size tracking, memory leak prevention
**Optimization:** Efficient string handling, minimal subprocess creation

## Security Architecture

### 1. Credential Protection
**Storage:** File-based with 600 permissions, optional keychain integration
**Transmission:** HTTPS only, no credential logging
**Display:** Token masking (first 4 + last 4 characters only)

### 2. Input Validation
**API Keys:** Format validation before use
**Configuration:** File permission validation, syntax checking
**User Input:** Sanitization of all user-provided parameters

### 3. Secure Development Practices
**Code Review:** Manual security review for all changes
**Dependency Management:** No external dependencies reduces attack surface
**Update Process:** Secure installation and update mechanisms

## Monitoring and Observability

### 1. Error Tracking
**Logging:** Detailed error messages without credential exposure
**Categorization:** Network errors, authentication failures, configuration issues
**User Communication:** Clear error messages with actionable resolution steps

### 2. Performance Monitoring
**Response Times:** Provider-specific performance tracking
**Success Rates:** API call success/failure ratios by provider
**Fallback Usage:** PPINFRA service utilization patterns

### 3. Usage Analytics (Future)
**Privacy Considerations:** Local-only analytics, no user data transmission
**Metrics:** Model usage patterns, provider preferences, error frequency
**Optimization:** Data-driven provider routing improvements

## Scaling Architecture

### 1. Horizontal Scaling (Users)
**Stateless Design:** No server-side state, per-user configuration
**Load Distribution:** Direct API calls, no central bottleneck
**Multi-Region:** Provider selection based on geographic availability

### 2. Provider Scaling
**Addition Framework:** Standardized process for new provider integration
**Configuration Templates:** Provider-specific configuration management
**Testing Strategy:** Automated testing across all providers

### 3. Feature Scaling
**Modular Design:** Feature isolation for independent development
**Configuration Complexity:** Progressive feature disclosure based on user sophistication
**Backward Compatibility:** Maintaining compatibility across major versions