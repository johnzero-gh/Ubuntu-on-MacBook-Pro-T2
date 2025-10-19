# Contributing Guidelines

Thank you for your interest in contributing to the Ubuntu on MacBook Pro T2 project! This document provides guidelines and standards for contributing.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How to Contribute](#how-to-contribute)
- [Documentation Standards](#documentation-standards)
- [Configuration Standards](#configuration-standards)
- [Testing Guidelines](#testing-guidelines)
- [Submission Process](#submission-process)

## Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inclusive environment for all contributors, regardless of:
- Experience level
- Technical background
- Personal identity or expression
- Nationality or origin

### Expected Behavior

- Be respectful and constructive in all communications
- Welcome newcomers and help them get started
- Accept constructive criticism gracefully
- Focus on what is best for the community
- Show empathy towards other community members

### Unacceptable Behavior

- Harassment, discrimination, or offensive comments
- Trolling or inflammatory remarks
- Spam or off-topic discussions
- Sharing others' private information without permission

## How to Contribute

### Types of Contributions

We welcome various types of contributions:

1. **Documentation Improvements**
   - Fix typos or unclear instructions
   - Add missing information
   - Improve examples
   - Translate documentation

2. **Configuration Files**
   - Share working configurations
   - Optimize existing configs
   - Add hardware-specific configs

3. **Bug Reports**
   - Report issues with documentation
   - Report incorrect information
   - Share compatibility problems

4. **Feature Requests**
   - Suggest new documentation topics
   - Request coverage of specific hardware
   - Propose workflow improvements

5. **Testing and Validation**
   - Test configurations on different hardware
   - Verify instructions work correctly
   - Report test results

## Documentation Standards

### File Organization

Follow this directory structure:

```
.
├── README.md              # Project overview and quick start
├── INSTALLATION.md        # Detailed installation guide
├── CONFIGURATION.md       # Configuration reference
├── HARDWARE.md           # Hardware compatibility
├── TROUBLESHOOTING.md    # Problem-solving guide
├── CONTRIBUTING.md       # This file
├── LICENSE               # License information
└── configs/              # Example configuration files
    ├── grub/
    ├── modprobe.d/
    ├── systemd/
    └── udev/
```

### Markdown Style

Follow these markdown conventions:

#### Headers

```markdown
# H1 - Document Title (once per file)
## H2 - Major Sections
### H3 - Subsections
#### H4 - Detailed Items
```

#### Code Blocks

Always specify the language for syntax highlighting:

```markdown
```bash
sudo apt update
sudo apt install package
```
```

#### Lists

Use consistent list formatting:

```markdown
- Unordered list item
  - Nested item
  - Another nested item
- Another item

1. Ordered list item
2. Second item
   - Can nest unordered in ordered
3. Third item
```

#### Links

Use descriptive link text:

```markdown
✅ Good: [T2 Linux Wiki](https://wiki.t2linux.org/)
❌ Bad: [Click here](https://wiki.t2linux.org/)
```

#### Tables

Align tables for readability:

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Data 1   | Data 2   | Data 3   |
| Data 4   | Data 5   | Data 6   |
```

### Content Guidelines

#### Be Clear and Concise

- Use simple, direct language
- Avoid jargon when possible
- Explain technical terms when necessary
- Use active voice

#### Be Specific

✅ Good:
```markdown
Install the T2 kernel modules:
```bash
sudo apt install linux-t2
```
```

❌ Bad:
```markdown
Install the necessary drivers.
```

#### Provide Context

Always explain why something is needed:

```markdown
## Install T2 Kernel Modules

The T2 chip requires custom kernel modules for hardware support.
Without these modules, WiFi, keyboard, and trackpad will not work.
```

#### Include Examples

Provide concrete examples for each instruction:

```markdown
### Configure GRUB

Edit the GRUB configuration:

```bash
sudo nano /etc/default/grub
```

Modify this line:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash intel_iommu=on iommu=pt"
```

Save and update:

```bash
sudo update-grub
```
```

## Configuration Standards

### File Naming

Use consistent naming for configuration files:

```
t2-<component>.conf        # For modprobe.d, sysctl.d
99-t2-<component>.rules    # For udev rules
t2-<service>.service       # For systemd services
```

Examples:
- `t2-audio.conf`
- `t2-wifi.conf`
- `99-t2-keyboard.rules`

### Configuration Format

#### Shell Scripts

```bash
#!/bin/bash
# Description: Brief description of script purpose
# Author: Your Name
# Date: YYYY-MM-DD

set -e  # Exit on error
set -u  # Exit on undefined variable

# Configuration variables
VARIABLE_NAME="value"

# Main function
main() {
    # Script logic here
    echo "Doing something..."
}

# Run main function
main "$@"
```

#### Configuration Files

Use clear comments:

```conf
# T2 Audio Configuration
# This configures the audio module for T2 chips

# Disable automatic microphone detection
options snd_hda_intel dmic_detect=0

# Disable power saving to prevent audio issues
options snd_hda_intel power_save=0
```

### Version Control

When modifying configuration files:

1. **Test thoroughly** before committing
2. **Document changes** in comments
3. **Provide rollback instructions**

Example:

```bash
# Modified: 2025-10-19
# Change: Disabled power saving for audio stability
# Previous value: power_save=1
# Rollback: Change power_save=0 back to power_save=1
options snd_hda_intel power_save=0
```

## Testing Guidelines

### Before Submitting

Test all changes:

1. **Fresh Installation Test**
   - Test on clean Ubuntu installation
   - Follow instructions exactly as written
   - Note any issues or unclear steps

2. **Hardware Compatibility**
   - List tested hardware models
   - Specify Ubuntu version used
   - Note any model-specific issues

3. **Configuration Validation**
   - Verify configurations work as expected
   - Test on multiple scenarios
   - Check for side effects

### Documentation Template for Testing

```markdown
## Test Report

**Date:** YYYY-MM-DD
**Tested by:** Your GitHub username
**Hardware:** MacBook Pro [model] [year]
**Ubuntu Version:** XX.XX
**Kernel Version:** X.X.X-t2

### Test Results

- [ ] Installation completed successfully
- [ ] WiFi working
- [ ] Audio working
- [ ] Keyboard working
- [ ] Trackpad working
- [ ] Suspend/resume working

### Issues Found

- None / List any issues

### Additional Notes

Any other relevant information
```

## Submission Process

### Step 1: Fork the Repository

1. Fork the repository on GitHub
2. Clone your fork locally:
   ```bash
   git clone https://github.com/YOUR-USERNAME/Ubuntu-on-MacBook-Pro-T2.git
   cd Ubuntu-on-MacBook-Pro-T2
   ```

### Step 2: Create a Branch

Create a descriptive branch name:

```bash
# For documentation updates
git checkout -b docs/update-audio-guide

# For configuration additions
git checkout -b config/add-wifi-optimization

# For bug fixes
git checkout -b fix/typo-in-readme
```

### Step 3: Make Changes

Follow the standards outlined in this document:

1. Make your changes
2. Test thoroughly
3. Update relevant documentation
4. Add examples if applicable

### Step 4: Commit Changes

Write clear commit messages:

```bash
# Good commit messages
git commit -m "docs: Add WiFi troubleshooting section"
git commit -m "config: Add optimized TLP configuration"
git commit -m "fix: Correct GRUB parameter in installation guide"

# Bad commit messages (avoid these)
git commit -m "Update"
git commit -m "Fixed stuff"
git commit -m "Changes"
```

### Commit Message Format

```
<type>: <subject>

<body (optional)>

<footer (optional)>
```

Types:
- `docs:` - Documentation changes
- `config:` - Configuration file changes
- `fix:` - Bug fixes
- `feat:` - New features
- `test:` - Testing updates
- `refactor:` - Code restructuring

Example:

```
docs: Add suspend/resume troubleshooting section

Added detailed troubleshooting steps for suspend/resume issues
on 16" MacBook Pro models. Includes kernel parameter changes
and module blacklisting options.

Tested on: MacBook Pro 16" (2019)
Ubuntu: 22.04 LTS
```

### Step 5: Push Changes

```bash
git push origin your-branch-name
```

### Step 6: Create Pull Request

1. Go to the original repository on GitHub
2. Click "New Pull Request"
3. Select your branch
4. Fill out the PR template:

```markdown
## Description

Brief description of changes

## Type of Change

- [ ] Documentation update
- [ ] Configuration addition/update
- [ ] Bug fix
- [ ] New feature

## Testing

Hardware tested:
- MacBook Pro [model] [year]

Ubuntu version:
- XX.XX

Test results:
- Describe what you tested
- Describe results

## Checklist

- [ ] I have tested these changes
- [ ] I have updated relevant documentation
- [ ] I have followed the documentation standards
- [ ] My changes don't break existing functionality
- [ ] I have added examples where appropriate

## Additional Context

Any other relevant information
```

### Step 7: Review Process

1. Wait for maintainer review
2. Address any feedback
3. Make requested changes
4. Update your PR

### Step 8: Merge

Once approved:
1. Maintainer will merge your PR
2. Your changes will be in the main branch
3. You'll be credited as a contributor!

## Style Guide

### Command Documentation

Always include:
1. What the command does
2. Why it's needed
3. Expected output
4. What to do if it fails

Example:

```markdown
### Update System

Update all packages to ensure latest security patches:

```bash
sudo apt update && sudo apt upgrade -y
```

Expected output: List of packages being upgraded

If update fails:
1. Check internet connection
2. Try `sudo apt update` alone first
3. Check `/var/log/apt/` for errors
```

### Warning Format

Use consistent warning styles:

```markdown
⚠️ **Warning:** This will erase all data on the disk. Backup first!

🔴 **Critical:** Do not interrupt this process or system may be unbootable.

💡 **Tip:** You can speed up this process by using a faster USB drive.

ℹ️ **Note:** This step is optional for dual-boot setups.
```

## Review Criteria

Pull requests will be evaluated on:

1. **Accuracy**: Information is correct and tested
2. **Clarity**: Instructions are clear and easy to follow
3. **Completeness**: All necessary information is included
4. **Formatting**: Follows documentation standards
5. **Testing**: Changes have been tested on actual hardware

## Getting Help

Need help contributing?

1. **Check existing documentation**: Read through current docs
2. **Search issues**: Look for similar questions
3. **Ask questions**: Open an issue with your question
4. **Join community**: 
   - [T2 Linux Discussions](https://github.com/t2linux/discussions)
   - [Ubuntu Forums](https://ubuntuforums.org/)

## Recognition

Contributors will be:
- Listed in the repository contributors page
- Credited in release notes
- Mentioned in acknowledgments

## License

By contributing, you agree that your contributions will be licensed under the same license as the project (MIT License).

## Questions?

If you have questions about contributing:
1. Open an issue with the `question` label
2. Tag it with `contributing`
3. We'll respond as soon as possible

Thank you for contributing to Ubuntu on MacBook Pro T2! 🎉
