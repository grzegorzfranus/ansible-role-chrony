# Changelog

All notable changes to this Chrony NTP role will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.7] - 2025-11-25

### Added ✅
- **New `chrony_rtc_settings` section** - Dedicated configuration group for Real-Time Clock settings
- **`rtcfile` support** - Added `rtcfile_enable` and `rtcfile_path` options for RTC drift tracking between reboots
- **`rtcdevice` option** - Added support for custom RTC device path specification
- **RTC validation** - Added assertion to warn/fail when `rtcautotrim` is enabled without `rtcfile` (per chrony documentation requirement)
- **Comprehensive RTC assertions** - Added validation for all new RTC settings

### Changed 🔄
- **BREAKING: Moved `rtcautotrim` from `chrony_hardware_settings` to `chrony_rtc_settings`** - This correctly groups RTC-related settings together
- **BREAKING: Moved `rtcsync` and `rtconutc` to `chrony_rtc_settings`** - Previously standalone variables, now part of the RTC settings group
- **Renamed `rtcautotrim` to `rtcautotrim_enable` + `rtcautotrim_interval`** - Separate enable flag from interval value for better control
- **Separated hardware timestamping from RTC settings** - `hwtimestamp` and `refclock` are now independent from RTC configuration
- **Changed default for `enable_hw_timestamp`** - Changed from `true` to `false` (hardware timestamping requires specific NIC support)
- **Reorganized template sections** - Added dedicated "Real-Time Clock (RTC) Configuration" section in chrony.conf

### Fixed 🔧
- **Fixed `rtcautotrim` being ineffective** - According to [chrony documentation](https://chrony-project.org/doc/3.4/chrony.conf.html), `rtcautotrim` is only effective with `rtcfile` directive. The role now properly requires `rtcfile_enable: true` for `rtcautotrim` to work
- **Fixed incorrect grouping** - `rtcautotrim` was incorrectly placed under hardware timestamp settings, which are unrelated to RTC trimming

### Removed ❌
- **Removed `chrony_rtcsync_enable`** - Replaced by `chrony_rtc_settings.rtcsync_enable`
- **Removed `chrony_rtconutc_enable`** - Replaced by `chrony_rtc_settings.rtconutc_enable`
- **Removed `chrony_hardware_settings.rtcautotrim`** - Replaced by `chrony_rtc_settings.rtcautotrim_enable` and `chrony_rtc_settings.rtcautotrim_interval`

### Migration Guide 📋
If upgrading from 1.0.6, update your variables:

```yaml
# OLD (1.0.6)
chrony_rtcsync_enable: true
chrony_rtconutc_enable: true
chrony_hardware_settings:
  rtcautotrim: 30

# NEW (1.0.7)
chrony_rtc_settings:
  rtcsync_enable: true
  rtconutc_enable: true
  rtcfile_enable: true        # Required for rtcautotrim to work!
  rtcfile_path: "/var/lib/chrony/rtc"
  rtcautotrim_enable: true
  rtcautotrim_interval: 30
```

## [1.0.6] - 2025-09-04

### Changed 🔄
- Normalized all task and handler names to `Chrony | action | description` (removed emojis)
- Updated tags in `tasks/main.yml` to use only allowed tags; replaced `prerequisites` with `requirements`

### Fixed 🔧
- Removed disallowed tags `check` and `never` to comply with role policy
- Verified handler wiring: `notify: restart chrony` matches handler `listen: restart chrony`

### Quality Improvements 📈
- Added a rationale comment near Chrony template deployment about lack of config validate command
- Confirmed lint passes with zero issues after changes

## [1.0.5] - 2025-08-06

### Fixed 🔧
- **Documentation Correction** - Fixed README.md to accurately reflect supported `chrony_ntp_source_mode` options
- **Variable Documentation** - Corrected documentation to show only 'pool' and 'server' modes are supported (not 'client')
- **Example Configurations** - Updated all configuration examples to use valid 'pool' mode instead of unsupported 'client' mode
- **Parameter Validation** - Documentation now matches actual assertion validation that only accepts 'pool' or 'server' modes
- **Ansible Linting Issues** - Fixed all 6 critical linting violations:
  - Replaced `systemctl` shell commands with `ansible.builtin.systemd` module in prerequisites.yml
  - Added FQCN `ansible.builtin.meta` instead of bare `meta` in test.yml
  - Added `changed_when: true` directive to command task in test.yml
  - Replaced `state: latest` with `state: present` in upgrade.yml for better idempotency
  - Fixed Jinja2 spacing issue in logrotate.yml template variable
- **Task Naming Consistency** - Added missing emojis to all task names for consistent visual organization

### Changed 🔄
- **README.md Examples** - All example configurations now use `chrony_ntp_source_mode: "pool"` instead of invalid 'client' mode
- **Variable Table** - Updated variable description to show correct options: 'pool', 'server' (removed invalid 'client' option)
- **Prerequisites Tasks** - Modernized service checking logic using systemd module instead of shell commands
- **Task Emojis** - Enhanced all task names with appropriate emojis following role standards:
  - 📦 for package installation
  - ⚙️ for configuration tasks
  - 🔍 for checking/verification tasks
  - 📁 for directory creation
  - 🛑 for service stopping
  - 🔄 for service restarts and upgrades

### Quality Improvements 📈
- **Linting Compliance** - Achieved perfect ansible-lint score with 0 failures, 0 warnings on 17 files
- **Production Profile** - Role now meets 'production' validation criteria in ansible-lint
- **YAML Standards** - Passed yamllint validation with no formatting issues
- **Module Modernization** - Updated to use modern ansible.builtin modules throughout
- **Idempotency Enhancement** - Improved task idempotency with proper change detection
- **Test Task Idempotency** - Fixed `chronyc makestep` task to only report changes when clock is actually stepped, not always marked as changed

## [1.0.4] - 2025-06-24

### Changed 🔄
- **Professional Workflow Naming** - Renamed `galaxy.yml` to `publish-to-galaxy.yml` for better clarity
- **CI Pipeline Rebranding** - Renamed `ci.yml` to `test-and-validation.yml` with professional naming
- **Enhanced CI/CD Presentation** - Added emoji indicators throughout all GitHub Actions workflows
- **Improved Job Organization** - Renamed workflow jobs for better semantic meaning and clarity
- **Step Name Clarity** - Enhanced step descriptions with more specific and professional language

### Fixed 🔧
- **CI Badge Reference** - Updated README.md CI status badge to point to renamed `test-and-validation.yml` workflow
- **Documentation Consistency** - Fixed broken badge link that was still referencing old `ci.yml` workflow file

### Workflow Improvements 🚀
- **Galaxy Publishing**: Changed to `📦 Publish to Ansible Galaxy` with descriptive emoji
- **Testing Pipeline**: Updated to `🧪 Test & Validation Pipeline` for professional appearance
- **Job Naming**: Consistent professional naming across all workflows
- **Step Emojis**: Added relevant emoji to all workflow steps:
  - 📥 Check out the codebase
  - 🐍 Set up Python 3
  - 📦 Install dependencies / Install Ansible dependencies
  - 🔍 Lint code
  - 🧪 Molecule test
  - 🚀 Upload role to Ansible Galaxy / Run molecule test

### Repository Organization 📁
- **Workflow Consolidation**: Two professionally named workflows for complete CI/CD pipeline
- **Consistency**: Aligned all workflow naming and emoji usage for cohesive presentation
- **Documentation**: Enhanced workflow readability and maintenance across the board
- **Badge Accuracy**: Ensured CI status badges reflect current workflow structure

### Handler System Improvements 🔄
- **Enhanced Handler Documentation** - Added comprehensive header with variables and handler triggers explanation
- **Handler Naming Convention** - Added 🔄 emoji to handler names for visual consistency
- **Handler Functionality** - Updated from `service` to `systemd` module for better control
- **Service Management** - Added `enabled` parameter for proper service state management
- **Listen Directives** - Added proper `listen` directives for handler triggers
- **Task Integration** - Updated task notify statements to use simplified handler names

### Task System Enhancements 🎯
- **Main Tasks Organization** - Added emoji to all main task orchestration steps:
  - 📂 OS-specific variables gathering
  - 🧪 Variable validation and requirements checking
  - 🛠️ System prerequisites verification
  - 📦 Package installation
  - 🌐 Service configuration
  - 📝 Logrotate configuration
  - 🔄 Package upgrade
  - 🧪 Time synchronization verification
- **Task Flow Consistency** - Changed `import_tasks` to `include_tasks` for validation tasks
- **Handler Integration** - Fixed notify statements in configure.yml and upgrade.yml tasks

### Validation System Overhaul 🧪
- **Comprehensive Assert Updates** - Enhanced all 29 validation tasks with emoji and improved messaging
- **Emoji Integration** - Added 🧪 emoji to all assertion task names for visual consistency
- **Enhanced Error Messages** - Replaced verbose multi-line messages with clear, concise error descriptions using ❌ emoji
- **Success Feedback** - Added success messages with ✅ emoji showing actual variable values
- **Improved User Experience** - Removed `quiet: true` for better feedback during execution
- **Variable Context** - Enhanced error messages to include variable values using `| default('undefined')`
- **Validation Categories** - Improved all validation sections:
  - General Settings Assertions (4 tasks)
  - Logrotate Settings Assertions (1 task)
  - NTP Server Settings Assertions (7 tasks)
  - Package and Service Settings Assertions (3 tasks)
  - File Path and Permission Assertions (5 tasks)
  - Advanced Tuning Assertions (6 tasks)

### Task Verification and Debugging Enhancements 🔍
- **Verification Tasks** - Added emoji to all checking and verification tasks across all task files
- **Status Checking** - Enhanced tasks with 🔍 emoji for file/service status checks
- **Testing Framework** - Improved test.yml with comprehensive emoji usage:
  - 🔄 Handler flushing
  - 🚀 Force synchronization
  - 🧪 Time synchronization verification
  - 🔍 Status checking
  - ✅ Results display
- **Prerequisites Enhancement** - Added emoji to prerequisite service checks
- **Configuration Verification** - Enhanced configuration file and directory verification tasks

### Code Quality and Maintainability 📈
- **Professional Task Naming** - Consistent emoji usage across all task files following industry standards
- **Enhanced Readability** - Improved visual organization of tasks with meaningful emoji indicators
- **Debugging Support** - Better task identification during execution with emoji visual cues
- **Error Handling** - Comprehensive error messages with context and suggested solutions
- **Documentation Alignment** - All task names and descriptions follow consistent patterns

### Molecule Testing Framework Enhancements 🧪
- **Testing Infrastructure Updates** - Comprehensive emoji integration across all molecule test files
- **Enhanced Test Preparation** - Improved prepare.yml with Chrony service availability checks and better permission handling
- **Professional Test Execution** - Updated converge.yml with visual feedback for package cache updates and system initialization
- **Comprehensive Verification** - Enhanced verify.yml with detailed assertions and professional status reporting
- **Visual Test Feedback** - Added emoji indicators throughout all testing phases:
  - 🔄 Package cache updates and system operations
  - ⏳ System initialization waiting periods
  - 🛠️ Permission fixes and setup tasks
  - 🔍 Verification and checking operations
  - 📊 Debug and display results
  - 🧪 Assertions and testing validations
  - ✅ Success confirmations
  - ❌ Error reporting
  - 🎯 Final verification summaries

### Testing Quality Improvements 🔬
- **Molecule Test Coverage** - Enhanced test scenarios with better error handling and success reporting
- **Professional Test Output** - Improved readability and debugging capabilities during test execution
- **Test Documentation** - Enhanced test file headers with clear execution flow documentation
- **Verification Enhancements** - Added comprehensive assertions for binary existence, configuration validation, and time accuracy
- **Status Reporting** - Professional test result summaries with visual indicators and detailed feedback

### Documentation Enhancements 📚
- **README.md Comprehensive Overhaul** - Complete restructuring following professional documentation standards
- **Feature Showcase** - Added ✨ Features section with 10 key capabilities and emoji indicators
- **Architecture Documentation** - Added 🎯 Architecture section with topology diagrams and mode explanations
- **Quick Start Guide** - Added 🚀 Quick Start with step-by-step setup instructions for client/server configurations
- **Configuration Examples** - Added ⚙️ Configuration section with default and advanced setup examples
- **Verification Procedures** - Added 🔍 Verification section with comprehensive testing and validation commands
- **Security Documentation** - Added 🛡️ Security Features section with 6 key security capabilities and configuration examples
- **Troubleshooting Guide** - Added 🔧 Troubleshooting section with common issues and resolution steps
- **File Structure Documentation** - Added 📁 File Structure with complete directory tree and file descriptions
- **CI/CD Integration Details** - Added 🔧 CI/CD Integration section with workflow descriptions and features
- **Enhanced Testing Documentation** - Expanded 🧪 Testing section with comprehensive test matrix and local testing instructions
- **Professional Formatting** - Applied consistent emoji usage throughout all sections following repository standards

### Technical Improvements Summary 🔧
- **Files Modified**: Updated 12+ files with comprehensive emoji integration (8 task files + 3 molecule files + README.md)
- **Validation Enhanced**: 29 assertion tasks improved with better error handling and user feedback
- **Handler System**: Complete overhaul with proper systemd integration and listen directives  
- **CI/CD Pipeline**: Professional workflow naming and emoji integration across GitHub Actions
- **Testing Framework**: Molecule test files enhanced with emoji and professional feedback
- **Documentation Framework**: Complete README.md overhaul with professional structure and comprehensive guides
- **User Experience**: Enhanced visual feedback with meaningful emoji indicators throughout execution, testing, and documentation
- **Maintainability**: Improved code organization and debugging capabilities across all components
- **Standards Compliance**: Aligned with repository emoji and English-only policies across all documentation and code
- **Professional Presentation**: Consistent emoji usage and professional formatting throughout the entire role
- **Backward Compatibility**: All changes maintain full functionality while enhancing presentation
