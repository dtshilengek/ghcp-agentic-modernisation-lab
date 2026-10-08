# Order Service — Assessment Artifact

> Source of truth for the modernization planning session. Produced from the verified local assessment run `20261008101640` (status: **passed**). Do not regenerate from scratch — reconcile against this file if a new assessment is run.

## 1. Current State

| Attribute | Value |
|---|---|
| Language / Runtime | Java 8 |
| Framework | Spring Boot 2.7.18 (Spring Framework — EOL for OSS support) |
| Build tool | Maven |
| Persistence | Spring Data JPA (`javax.persistence.*`), H2 (runtime) |
| Web layer | Spring MVC, `WebConfig` extends deprecated `WebMvcConfigurerAdapter` |
| Containerization | None — no Dockerfile present |
| Assessment run id | `20261008101640` |
| Verification status | ✅ passed (`artifactValidation: passed`) |

## 2. Assessment Summary (AppCAT + CVE/CWE scans)

- Total projects analyzed: 1
- Total issues: 10 (18 underlying incidents)
- Total estimated effort: 108 story points
- Severity breakdown: 8 mandatory, 6 optional, 4 potential, 0 informational
- Category breakdown: framework-upgrade (6), java-version-upgrade (5), spring-migration (2), containerization (1), jakarta-migration (1), local-credential (1), local-resource-access (1), remote-communication (1)

### 2.1 AppCAT / Migration Findings

| Severity | Rule | Finding |
|---|---|---|
| MANDATORY | spring-boot-to-azure-spring-boot-version-01000 | Spring Boot version has reached end of OSS support |
| MANDATORY | spring-framework-version-01000 | Spring Framework version has reached end of OSS support |
| MANDATORY | dockerfile-00000 | No Dockerfile found (containerization gap) |
| MANDATORY | hardcoded-ip-address | Hardcoded IP address detected |
| OPTIONAL | azure-java-version-02000 | Java version is not the latest LTS |
| OPTIONAL | localhost-00004 | Localhost usage detected |
| POTENTIAL | azure-password-01000 | Password found in a configuration file |
| POTENTIAL | jakarta-database-00002 | Usage of `javax.persistence` (Jakarta Persistence) APIs — needs `jakarta.*` migration |
| POTENTIAL | spring-boot-to-azure-port-01000 | Server port configuration found |
| POTENTIAL | spring-boot-to-azure-restricted-config-01000 | Restricted configuration(s) found |

### 2.2 Security Findings (CVE)

| Severity | Advisory | Component | Fixed in |
|---|---|---|---|
| MANDATORY | GHSA-jfh8-c2jp-5v3q — Remote code injection in Log4j (Log4Shell) | log4j-core 2.14.1 | 2.15.0 |
| MANDATORY | GHSA-7rjr-3q55-vv33 — Incomplete fix for Apache Log4j vulnerability | log4j-core 2.14.1 | 2.16.0 |
| MANDATORY | GHSA-p6xc-xr62-6r2g — Apache Log4j2 Uncontrolled Recursion (DoS) | log4j-core 2.14.1 | 2.17.0 |
| MANDATORY | GHSA-22wj-vf5f-wrvj — Password exposure in H2 Database | h2 | 2.2.220 |
| MANDATORY | GHSA-599f-7c49-w659 — Arbitrary code execution in Apache Commons Text (Text4Shell) | commons-text 1.9 | 1.10.0 |
| POTENTIAL | GHSA-8489-44mv-ggj8 — Improper Input Validation and Injection in Apache Log4j2 | log4j-core 2.14.1 | 2.17.1 |
| POTENTIAL | GHSA-vc5p-v9hr-52mj — Apache Log4j TLS hostname not verified in Socket Appender | log4j-core 2.14.1 | 2.25.3 |
| POTENTIAL | GHSA-6hg6-v5c8-fphq — Apache Log4j Core verifyHostName silently ignored in TLS config | log4j-core 2.14.1 | 2.25.4 |
| POTENTIAL | GHSA-3pxv-7cmr-fjr4 — Apache Log4j Core silent log event loss in XmlLayout | log4j-core 2.14.1 | (minor) |

### 2.3 Code Quality (CWE)

| Severity | Finding | Location |
|---|---|---|
| POTENTIAL | CWE-477 — Use of Obsolete Function: `WebConfig` extends deprecated `WebMvcConfigurerAdapter` | `src/main/java/com/contoso/demo/orderservice/config/WebConfig.java` |

All other 53 CWE rules scanned across 6 domains (code-quality, concurrency/synchronization, credentials/secrets, file-path security, injection, memory safety) returned **NOT_FOUND**.

## 3. Validated Target State (Accepted)

The following target state and recommendations were reviewed and **accepted** as the modernization destination for this service:

- **Runtime**: Java 8 → **Java 25** (latest LTS)
- **Framework**: Spring Boot 2.7.18 → **Spring Boot 4.x** (and Spring Framework 7.x), the only combination compatible with Java 25 per the supported compatibility matrix
- **API namespace**: `javax.*` → **`jakarta.*`** (Jakarta EE migration), required by Spring Boot 4.x / Jakarta EE 10+
- **Web config**: Replace deprecated `WebMvcConfigurerAdapter` with `WebMvcConfigurer` (removes CWE-477)
- **Dependency remediation**: log4j-core, commons-text, and h2 upgraded to patched versions to close all 9 CVEs above
- **Not in accepted scope (open/deferred)**: containerization (Dockerfile), hardcoded IP address remediation, localhost usage, externalizing the configuration-file password to Azure Key Vault / passwordless auth, and server-port / restricted-configuration adjustments for Azure App Service/AKS/Container Apps — these were flagged by AppCAT but not yet actioned or scoped into an executed task.

## 4. Status of Already-Executed Work

**IMPORTANT for the planning session**: Part of the accepted target state has **already been implemented** on branch `modernize/java-20261008104714` (commits `ca86f3f`, `39edd45`):

- ✅ Java 8 → Java 25 upgrade (compiler source/target, `java.version`)
- ✅ Spring Boot 2.7.18 → 4.1.1 upgrade
- ✅ Jakarta migration (`javax.persistence.*`, `javax.validation.*` → `jakarta.*`) in `Order.java`, `OrderController.java`
- ✅ `WebConfig` deprecated API fix (`WebMvcConfigurerAdapter` → `WebMvcConfigurer`)
- ✅ CVE remediation: log4j-core → 2.25.5, commons-text → 1.14.0, h2 → 2.4.240 (BOM-managed)
- ✅ `mvn clean test` — BUILD SUCCESS, 20/20 tests passed (validated twice, after each task)

The planning session should **verify these items are still current** (re-run the assessment or build if needed) rather than re-planning them, and focus prioritization on the **remaining/deferred items** in section 3, plus any new risks surfaced by a fresh look at the codebase (e.g., deployment/containerization readiness, hardcoded IP, password externalization).

## 5. Source Artifacts (for traceability)

- Canonical report: `.github/modernize/assessment/reports/report-20261008101640/report.json`
- Verification receipt: `.github/modernize/assessment/reports/report-20261008101640/verification.json` (status: passed)
- Interactive HTML report: `.github/modernize/reports/20261008101640-assess.html` (`latest.html`)
- Existing plan (already executed for the upgrade + security portions): `.github/modernize/java25-springboot4-modernization/plan.md` and `.metadata/tasks.json`