# System Patterns: Architecture Decisions

## Core Architecture Patterns

### 1. Environment Variable Injection Pattern
**Purpose:** Seamless Claude Code IDE integration through environment variables
**Implementation:** `export ANTHROPIC_BASE_URL=...`, `export ANTHROPIC_AUTH_TOKEN=...`
**Benefits:** Native IDE integration, zero modification to existing workflows
**Trade-offs:** Relies on IDE's environment variable support, potential auth conflicts

### 2. Multi-Tier Configuration Hierarchy
**Purpose:** Flexible configuration supporting multiple deployment scenarios
**Priority Order:** Environment Variables → Config File → Built-in Defaults
**Implementation:** `is_effectively_set()` function validates configuration
**Benefits:** CI/CD friendly, zero-config support, advanced user customization

### 3. Intelligent Fallback Mechanism
**Purpose:** Reliability through multiple service integration points
**Pattern:** Official API → PPINFRA Service → Experience Keys
**Implementation:** Provider-specific routing with error handling
**Benefits:** High availability, cost optimization, user experience continuity

### 4. Zero-Config Experience Key Pattern
**Purpose:** Immediate value without user configuration overhead
**Implementation:** Built-in keys for PPINFRA service integration
**Benefits:** Instant onboarding, progressive conversion to paid services
**Strategic Value:** User acquisition channel with minimal friction

## Integration Patterns

### 1. Multi-Provider Abstraction Layer
**Pattern:** Unified interface for diverse AI provider APIs
**Implementation:** Provider-specific functions with common environment variable output
**Benefits:** Simple user interface, complex provider diversity
**Scalability:** Easy provider addition through standardized pattern

### 2. Account Management System
**Pattern:** Multi-account support for usage limit optimization
**Implementation:** Claude Pro account switching with macOS Keychain integration
**Benefits:** Cost optimization for power users, enterprise governance
**Security:** Encrypted credential storage with secure sharing

### 3. Localization Architecture
**Pattern:** JSON-based multi-language support system
**Implementation:** Dynamic loading based on system locale, fallback to English
**Benefits:** Global user support, contribution-friendly translation process
**Maintenance:** Centralized translation management with key-based system

## Security Patterns

### 1. Token Masking Pattern
**Implementation:** Display only first 4 + last 4 characters in status output
**Purpose:** Prevent accidental credential exposure while providing verification
**Validation:** Never print full tokens in any user-facing output

### 2. Secure Configuration Management
**Pattern:** File permissions (600) and environment variable precedence
**Implementation:** Config file creation with secure defaults, env var override capability
**Purpose:** Support for both interactive and automated deployment scenarios

### 3. Keychain Integration Pattern
**Purpose:** Secure credential storage with system integration
**Implementation:** macOS Keychain for account credentials, local backup for reliability
**Benefits:** System-level security, user convenience, backup reliability

## Deployment Patterns

### 1. Dual Execution Model
**Pattern:** Direct execution + installed shell functions
**Implementation:** Script-based usage + installation with shell injection
**Benefits:** Zero-install testing + persistent convenience, supports diverse usage patterns

### 2. Idempotent Installation Pattern
**Purpose:** Safe repeated installation without side effects
**Implementation:** Check-before-write patterns, shell function detection
**User Experience:** Confident installation and uninstallation processes

## Development Patterns

### 1. Spec-Driven Development Workflow
**Pattern:** EARS requirements → Technical design → TDD tasks → Implementation
**Purpose:** Systematic development with explicit approval gates
**Benefits:** Quality assurance, stakeholder alignment, reduced rework

### 2. Agent-Based Specialization
**Pattern:** Specialized agents for specific development tasks
**Implementation:** code-reviewer, expert-advisor, tech-proposal-reviewer agents
**Purpose:** Domain expertise application, consistent quality standards

### 3. Memory Bank System Integration
**Pattern:** Structured context management across development sessions
**Purpose:** Persistent project understanding, consistent development decisions
**Implementation:** Hierarchical markdown files with clear dependency relationships

## Scaling Patterns

### 1. Provider Extension Framework
**Pattern:** Standardized process for adding new AI providers
**Implementation:** Configuration templates, translation key addition, case statement extension
**Benefits:** Consistent user experience across providers, maintainable codebase

### 2. Fallback Service Evolution
**Pattern:** Progressive enhancement of backup services
**Current:** PPINFRA integration with experience key support
**Future:** Multi-provider fallback, intelligent routing optimization

### 3. Enterprise Feature Framework
**Pattern:** Layered feature access based on user sophistication
**Implementation:** Basic features for all users, advanced features for power users
**Business Model:** Freemium conversion pathway, enterprise feature differentiation

## Error Handling Patterns

### 1. Graceful Degradation Pattern
**Purpose:** Maintain user productivity despite service failures
**Implementation:** Automatic fallback to alternative services, clear user communication
**Benefits:** Reliability, user trust, service diversity resilience

### 2. Comprehensive Status Reporting
**Pattern:** Detailed but secure status information display
**Implementation:** Masked tokens, service availability indicators, configuration validation
**User Experience:** Clear visibility into system state without security compromise

### 3. Proactive Configuration Validation
**Purpose:** Prevent user frustration through early issue detection
**Implementation:** Configuration file validation, API key format checking, service availability testing
**Benefits:** Improved onboarding experience, reduced support burden

## CodeCMD Integration Patterns

### 1. Aggregator Platform Abstraction Pattern
**Purpose:** Multi-model aggregator support through single integration point
**Implementation:** Single CodeCMD API endpoint with internal model routing
**Benefits:** Model diversity without individual provider integration complexity
**Pattern:** `switch_to_codecmd()` → CodeCMD API → Internal model selection

### 2. Regional Optimization Pattern
**Purpose:** Performance optimization for specific geographic markets
**Implementation:** Region-specific endpoint selection and routing logic
**Benefits:** Reduced latency, improved reliability for target markets
**Application:** Chinese market optimization through CodeCMD's regional infrastructure

### 3. Advanced Model Access Pattern
**Purpose:** Early access to cutting-edge models before direct API availability
**Implementation:** Aggregator partnership for model preview access
**Benefits:** Competitive differentiation, technology leadership positioning
**Examples:** GPT-5-Codex, Factory Sonnet 4 access through CodeCMD partnership

### 4. Multi-Aggregator Architecture Pattern
**Purpose:** Extensible framework for multiple aggregator platforms
**Implementation:** Standardized aggregator integration interface with provider-specific implementations
**Benefits:** Scalable ecosystem expansion, consistent user experience across aggregators
**Future Proof:** Ready for additional aggregator partnerships beyond CodeCMD