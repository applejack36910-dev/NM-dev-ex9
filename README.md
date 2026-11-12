# NM-dev-ex9

## Central CI Pipeline – Jenkins Configuration

### 1. Checkout
- New Item → Freestyle Project → `Checkout`
- Source Code Management → Git
- Repository URL: `https://github.com/Karkuzhali369/NM-dev-ex9.git`
- Branch: `*/main`
- Post-build Action → Build other projects → `Compile`
- Trigger: Only if build is stable.

### 2. Compile
- New Item → Freestyle Project → `Compile`
- Source Code Management → Git (same repository and branch)
- Build Steps → Invoke top-level Maven targets
- Maven: `Maven-3.9.16`
- Goals:
```bash
clean compile
```
- Post-build Action → Build other projects → `UnitTest`

### 3. UnitTest
- New Item → Freestyle Project → `UnitTest`
- Source Code Management → Git (same repository and branch)
- Build Steps → Invoke top-level Maven targets
- Maven: `Maven-3.9.16`
- Goals:
```bash
test
```
- Post-build Action → Publish JUnit test result report
- Test report XML:
```text
**/target/surefire-reports/*.xml
```
- Build other projects → `Coverage`

### 4. Coverage
- New Item → Freestyle Project → `Coverage`
- Source Code Management → Git (same repository and branch)
- Build Steps → Invoke top-level Maven targets
- Maven: `Maven-3.9.16`
- Goals:
```bash
clean verify
```
- Post-build Action → Publish HTML reports
  - HTML directory: `target/site/jacoco`
  - Index page: `index.html`
  - Report title: `JaCoCo Coverage Report`
- Build other projects → `StaticAnalysis`

### 5. StaticAnalysis
- New Item → Freestyle Project → `StaticAnalysis`
- Source Code Management → Git (same repository and branch)
- Build Steps → Invoke top-level Maven targets
- Maven: `Maven-3.9.16`
- Goals:
```bash
clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:5.0.0.4389:sonar -Dsonar.projectKey=devops_demo -Dsonar.projectName=devops_demo -Dsonar.host.url=http://localhost:9000 -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml-Dsonar.token=sqp_423e98e963650919f3103fca0d4011aa96f39c87
```
- SonarQube token: Jenkins Credentials (`SONAR_TOKEN`).

### 6. Central Pipeline View
- Dashboard → `+` → Build Pipeline View
- View Name: `devops_demo_CI_Pipeline`
- Initial Job: `Checkout`
- Number of displayed builds: `5`
- Save.

### Execution Flow

```text
Checkout
   ↓
Compile
   ↓
UnitTest
   ↓
Coverage
   ↓
StaticAnalysis
```

**Note:** All jobs use the same GitHub repository and `main` branch. Connect jobs using **Build other projects → Trigger only if build is stable**.
