# Security Verification Tests (SVTs) in CICD Pipeline

![Security_In_CICD](../Images/Security_In_CICD.jpg)

The CICD components of the AzSDK enable a dev ops team to consistently replicate a secure baseline
configuration of their cloud subscription and resources across multiple deployment environments. 
This ensures that the secure baseline established during development is enforced in a consistent and
automated fashion across other higher environments such as UAT and Prod.


The following features are currently included:
- [Build/Release Tasks for CICD workflows](Security_In_CICD_userguide.md)  - Integrates security testing within the VSTS CICD 
pipeline via VSTS build/release extensions (tasks) for security verification tests (SVTs) and other security tools.

> **Notes** 
> - We also plan to support miniaturized ARM templates which can be combined with existing templates and, subsequently, to analyze 'ARM template equivalence' from a security standpoint.
> - We have built a PoC for a similar CICD extension for Jenkins for AzSDK. Documentation for the Jenkins extension will be added soon.
 <!-- #TODO# Jenkins doc --> 
 
