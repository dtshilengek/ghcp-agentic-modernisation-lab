# Modernization Plan: Order Service Java 25 and Spring Boot 4 Modernization

**Project**: order-service

---

## Technical Framework

- **Language**: Java 8
- **Framework**: Spring Boot 2.7.18
- **Build Tool**: Maven 3.10.0
- **Database**: H2 (in-memory, runtime only) via Spring Data JPA
- **Key Dependencies**: Spring Data JPA, Spring Web, Spring Validation, Apache Log4j Core 2.14.1, Apache Commons Text 1.9, H2 Database 2.1.214

---

## Overview

This migration upgrades Order Service from its current Java 8 / Spring Boot 2.7.18 stack, which has reached
end of OSS support, to Java 25 / Spring Boot 4.x. The new stack will:

- Move the runtime to Java 25 and Spring Boot 4.x (Spring Framework 7.x), including the mandatory
  Jakarta EE migration (`javax.*` → `jakarta.*`), restoring the application to a fully supported baseline.
- Remediate all known CVEs identified in the project's direct dependencies, including the critical
  Log4Shell family in Log4j Core, the Text4Shell vulnerability in Apache Commons Text, and a high-severity
  password exposure issue in the H2 database driver.
- Replace the deprecated `WebMvcConfigurerAdapter` with the modern `WebMvcConfigurer` interface as part of
  the framework upgrade.

The migration is an in-place runtime/dependency modernization; no Azure service migration, containerization,
or deployment was requested, so this plan is scoped to the upgrade and security remediation tasks only.

---

## Migration Impact Summary

| Application   | Original Service      | New Azure Service | Authentication | Comments                                   |
|---------------|------------------------|--------------------|-----------------|---------------------------------------------|
| order-service | Java 8 / Spring Boot 2.7.18 (in-place) | N/A (no Azure migration requested) | N/A | In-place upgrade to Java 25 / Spring Boot 4.x with CVE remediation |

---

## Open Questions & Questionnaire

None. The requested Java 25 + Spring Boot upgrade maps directly to the Spring Boot 4.x upgrade task
(Spring Framework 7.x, Jakarta EE, JDK 25), which is the latest supported combination, so no clarification
was required.