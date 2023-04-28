# 3.2 CI/CD Pipeline Enhancements

## Introduction

> This exercise is an **optional** stretch goal. If you are short on time, you may wish to work on this exercise after the workshop. 

In this exercise, we will be extending the CI/CD pipeline you created in the [previous exercise](./Docker-Images) to add several automated tools.

These include:

1. Code Quality Scanning
2. SAST Scanning
3. Container Scanning
4. DAST Scanning

Leveraging on these tools allows Developers to detect and prevent defects in their codebase earlier in the Software Development Lifecycle (SDLC) more frequently. This is known as "Shift-Left Testing" in DevOps, and it fully embodies the "Test early and often" mantra typically seen in Agile organizations practicing iterative development.

## Prerequisites

You should have completed all previous exercises:

1. [DevOps Setup](/docs/ci-cd/DevOps-Setup) 
2. [Docker Images and CI/CD](/docs/ci-cd/Docker-Images)
3. [Deployment to Cloud](/docs/infrastructure/Infrastructure-Setup)

## Enhancing the CI/CD Pipeline

### Code Quality

To ensure your project’s code stays simple, readable, and easy to contribute to, you can use GitLab CI/CD to analyze your source code quality.

For example, while you’re implementing a feature, you can run Code Quality reports to analyze how your improvements are impacting your code’s quality. You can use this information to ensure that your changes are improving performance rather than degrading it.

Code Quality help to flag out:

- Non-adherence to coding standards and conventions
- Common logical bugs / errors
- Hard-to-maintain patterns

#### Stretch Goal 1

Add a new job in `.github-ci.yml` to introduce Code Quality Checks to the codebase.

Include the Code Quality Template in your CI Configuration, Code Quality job will default to run on the `test` stage, so ensure that test stage is included in your CI configuration

```
include:
  - template: Code-Quality.gitlab-ci.yml
```

Requirements:

1. Flag out code quality issues in the frontend and backend source code
2. Have a look at how **Code Quality Scan** works [here](https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Jobs/Code-Quality.gitlab-ci.yml).

### SAST(Static Application Security Testing) Scanning

If you’re using GitLab CI/CD, you can use Static Application Security Testing (SAST) to check your source code for known vulnerabilities. You can run SAST analyzers in any GitLab tier. The analyzers output JSON-formatted reports as job artifacts.

SAST Scan helps to flag out:

- Code that has a potentially dangerous attribute in a class, or unsafe code that can lead to unintended code execution.
- Your application is vulnerable to cross-site scripting (XSS) attacks that can be leveraged to unauthorized access to session data.

#### Stretch Goal 2

Add a new job in `.github-ci.yml` to introduce static code scanning to the codebase.

Include the SAST Scan Template in your CI Configuration, SAST job will default to run on the `test` stage, so ensure that test stage is included in your CI configuration

```
stages:
  - test

include:
  - template: Security/SAST.gitlab-ci.yml
```

Requirements:

1. Flag out source code vulnerabilities and observe its severity.
2. Have a look at how **SAST Scan** works [here](https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Jobs/SAST.gitlab-ci.yml).

### Container Scanning

In almost all projects small or large, you will likely depend on external libraries to develop your applications. You may take a look at the dependencies required by the frontend and backend in the respective `package.json` files.

Even the most widely used dependencies may contain vulnerabilities. Vulnerabilities in dependencies may expose your application to potential exploitation and affects its security posture. It is important to keep all dependencies up-to-date so that these security risks are mitigated.

However, it is tedious to perform these checks manually, especially for larger projects that utilise many external libraries. It is also difficult to be thorough, since the libraries may also depend on other libraries that could contain vulnerabilities.

This process of scanning your application for such vulnerabilities can be fully-automated with the help of a CI/CD pipeline and image scanning tools. When vulnerabilities are discovered and publicly disclosed, they are usually assigned a [Common Vulnerabilities and Exposures glossary (CVE)](https://cve.mitre.org/) entry with a unique ID. Whenever the pipeline runs, these automated tools reference known CVEs to detect vulnerable dependencies in your application and propose remediation measures.

Some tools may also include additional compliance checks to ensure that applications adhere to industry-recognized benchmarks or best practices, such as the [CIS Docker Benchmarks](https://www.cisecurity.org/benchmark/docker/).

#### Stretch Goal 3

Add a new job in `.gitlab-ci.yml` to automatically scan the frontend and backend images for vulnerabilities.

Include the Container Scanning Template in your CI Configuration, Container Scanning job will default to run on the `test` stage, so ensure that test stage is included in your CI configuration

```
stages:
  - test

include:
  - template: Security/Container-Scanning.gitlab-ci.yml
```

Requirements:

1. Flag out vulnerabilities found in the images
2. Have a look at how **Container Scanning** works [here](https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Security/Container-Scanning.gitlab-ci.yml).

There are several open-source Security scanning Gitlab tools available to accomplish this task. You may consider using the [Gitlab Security Scan Templates](https://gitlab.com/gitlab-org/gitlab/-/tree/master/lib/gitlab/ci/templates/Security) for more Security Scan coverage.

### DAST(Dynamic Application Security Testing) Scanning

If you deploy your web application into a new environment, your application may become exposed to new types of attacks. For example, misconfigurations of your application server or incorrect assumptions about security controls may not be visible from the source code.

Dynamic Application Security Testing (DAST) examines applications for vulnerabilities like these in deployed environments. DAST uses the open source tool OWASP Zed Attack Proxy for analysis.

#### Stretch Goal 4

Add a new job in `.gitlab-ci.yml` to automatically scan the newly deployed web application.

Include the DAST Template in your CI Configuration, DAST Scanning job will default to run on the `dast` stage, so ensure that dast stage is included in your CI configuration.

```
include:
  - template: DAST.gitlab-ci.yml

stages:
  - dast

variables:
  DAST_WEBSITE: https://example.com
```

Requirements:

1. Analyse report produced on your application
2. Have a look at how **DAST Scanning** works [here](https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Security/DAST.gitlab-ci.yml).

DAST can analyze applications in two ways:
1. Passive scan only (DAST default). DAST executes [ZAP’s Baseline Scan](https://www.zaproxy.org/docs/docker/baseline-scan/) and doesn’t actively attack your application.
2. Passive and active scan. DAST can be [configured](https://docs.gitlab.com/ee/user/application_security/dast/#full-scan) to also perform an active scan to attack your application and produce a more extensive security report. It can be very useful when combined with Review Apps.

---

Congratulations on completing the stretch goals! Do remember to move on to the [Final](/docs/infrastructure/CleanUp)  exercise to clean up your cloud environment.

---