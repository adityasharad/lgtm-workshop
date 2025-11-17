# lgtm-workshop

> **⚠️ REPOSITORY STATUS: INACTIVE / BEST EFFORT**
>
> This repository is no longer actively maintained. It was created for workshops on lgtm.com, which has been decommissioned.
>
> **For current security analysis, please use [GitHub code scanning](https://docs.github.com/en/code-security/code-scanning/introduction-to-code-scanning/about-code-scanning) and [GitHub Advanced Security](https://docs.github.com/en/get-started/learning-about-github/about-github-advanced-security).**

## About This Repository

This repository contains **deliberately vulnerable code** for security analysis demonstrations and educational purposes. The code includes intentional security vulnerabilities to showcase how static analysis tools can identify security issues.

**⚠️ WARNING:** This codebase contains known security vulnerabilities by design. Do not use this code in production environments.

## Included Projects

The following open source projects are included, licensed under the Apache License 2.0:
* Apache ActiveMQ (based on `e8a0d042d8897cffd904697ac8febae7824ddc18`)
* Apache Qpid JMS (based on `b05d577c1ae3f55d41a3d53cd4346951118dd06d`)

The source has been modified to include variants of JMS `ObjectMessage`s with deserialization vulnerabilities, based on current `master` for each project at the time of creation.

## Alternative Tools

Since lgtm.com has been decommissioned, consider these alternatives for security analysis:
* [GitHub Code Scanning](https://docs.github.com/en/code-security/code-scanning) - Native GitHub security analysis using CodeQL
* [CodeQL](https://codeql.github.com/) - The query language and analysis engine that powered lgtm.com
* [GitHub Advanced Security](https://docs.github.com/en/get-started/learning-about-github/about-github-advanced-security) - Comprehensive security features for GitHub repositories