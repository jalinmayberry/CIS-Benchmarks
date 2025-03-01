# CIS-Benchmarks

# System Hardening Lab

Welcome to the System Hardening Lab! This project guides you through securing various systems to meet Center for Internet Security (CIS) compliance standards. Using security assessment tools and automation, you'll transform a baseline system into a hardened environment that satisfies industry security benchmarks.

## Project Overview

This lab is designed to provide hands-on experience with enterprise-grade security hardening processes. You'll learn how to audit systems for compliance gaps, remediate security vulnerabilities using automation, and verify the effectiveness of implemented controls.

## Project Structure

The lab is organized into two primary parts:

- **Level 1 Hardening:** Basic compliance with security essentials that cause little to no service interruptions and is suitable for servers and workstations in most environments.
    - Performing baseline security audits
    - Generating remediation playbooks
    - Applying fixes through automation
    - Verifying compliance through post-remediation audits
- **Level 2 Hardening:** Advanced compliance (Defense-in-Depth) for environments requiring heightened security, but introduces a bit of reduced functionality.
    - Extending security controls beyond Level 1 requirements
    - Implementing more restrictive configurations
    - Managing potential operational impacts

## Key Components

- **Security Audit/Assessment Tools** for compliance evaluation (i.e OpenSCAP)
- **Automation Tools** for systematic remediation (i.e Ansible)
- **[CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)** for standardized security controls


## ⚠️ Risk Considerations

<aside>
**IMPORTANT: READ BEFORE PROCEEDING**

These system hardening procedures involve significant changes to your operating system configuration. Please be aware of the following risks and considerations:

</aside>

**1. System Access Impacts:**

- The hardening process may disable privileged logins and implement strict authentication policies
- You may be locked out of user accounts due to tightened authentication requirements
- Always maintain an alternative access method or recovery plan before proceeding

**2. Service Availability:**

- Security hardening may disable services or change default configurations
- Some applications may stop functioning after security controls are implemented
- Critical ports may be closed by firewall implementation

**3. Recovery Preparation:**

- CREATE SYSTEM SNAPSHOTS before beginning the hardening process
- Document your current system configuration for reference
- Be familiar with the emergency access procedures provided in this guide

**4. Testing Environment:**

- Always perform this procedure in a testing environment first
- Verify all critical functionality after hardening before applying to production systems

**5. Custom Configurations:**

- The provided scripts implement generic CIS benchmarks that may need customization
- Your specific environment may require exceptions to certain security controls
- Always review the generated remediation plans before execution

## Emergency Recovery

If you are locked out of your system after hardening, follow the emergency recovery procedure detailed in the specific system guide section of the main document. Different operating systems have different recovery methods, but they typically involve:

1. Interrupting the boot process
2. Modifying boot parameters to gain emergency access
3. Resetting credentials and security contexts
4. Performing system recovery

## Disclaimer
This project is intended for educational and informational purposes only. The documentation, scripts, and configurations provided here are meant to assist users in understanding and setting up environments involving open-source and proprietary tools such as Red Hat Enterprise Linux, Ansible, and OpenSCAP. This repository is not endorsed by or affiliated with any of these companies.

Please note:
- This repository does not include proprietary software. Any usage of such software requires the appropriate licenses from the respective vendors.
- The provided code and configurations are intended to work in isolated or test environments. Use in production environments is at your own risk.
- The MIT License in this repository applies only to the documentation and code provided here and does not extend to any third-party software or services mentioned.

By using this project, you agree to do so at your own discretion and risk. Please ensure compliance with any applicable terms and conditions of the software vendors mentioned.
