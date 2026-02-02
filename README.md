# SonarQube_FOr_DotNet


# **Guide: Setting up SonarQube and Scanning OWASP WebGoat .NET Project**

## **1. Downloading and Setting Up SonarQube**

1. Go to the official SonarQube website: [https://www.sonarqube.org/downloads/](https://www.sonarqube.org/downloads/).
2. Download **SonarQube Community Edition**.
3. Extract the downloaded zip to a folder (e.g., `C:\SonarQube`).
4. Start SonarQube:

   * Navigate to `C:\SonarQube\bin\windows-x86-64\`.
   * Run `StartSonar.bat`.
   * Wait for the server to start.
5. Open browser and access the dashboard: [http://localhost:9000](http://localhost:9000).
6. Log in with default credentials: `admin` / `admin`.
7. Generate a **user token** for analysis:

   * Go to `My Account → Security → Generate Tokens`.
   * Copy token for later use.

---

## **2. Downloading SonarScanner for .NET**

1. Go to the official page: [https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-msbuild/](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-msbuild/)
2. Download **SonarScanner for MSBuild** (for .NET Framework projects) or use `dotnet tool install --global dotnet-sonarscanner` for .NET Core projects.
3. Add the scanner folder to your `PATH` (optional, but convenient).

---

## **3. Preparing the OWASP WebGoat .NET Project**

1. Download OWASP WebGoat .NET from GitHub: [https://github.com/WebGoat/WebGoat.NET](https://github.com/WebGoat/WebGoat.NET)
2. Open **Developer Command Prompt** or normal CMD.
3. Navigate to the project folder:

   ```cmd
   cd C:\Users\YourName\Desktop\codeReview\OWASP-WebGoat.NET
   ```

---

## **4. Running SonarScanner on the Project**

### **.NET Core Projects**

```cmd
dotnet sonarscanner begin /k:"webGoat" /d:sonar.host.url="http://localhost:9000" /d:sonar.token="YOUR_TOKEN"
dotnet build
dotnet sonarscanner end /d:sonar.token="YOUR_TOKEN"
```

### **.NET Framework Projects**

```cmd
SonarScanner.MSBuild.exe begin /k:"webGoat" /d:sonar.host.url="http://localhost:9000" /d:sonar.token="YOUR_TOKEN"
"C:\Program Files\Microsoft Visual Studio\2022\Community\MSBuild\Current\Bin\MSBuild.exe" WebGoat.NET.sln /t:Rebuild
SonarScanner.MSBuild.exe end /d:sonar.token="YOUR_TOKEN"
```

---

## **5. Problems Faced and Solutions**

| Problem                                              | Cause                                                                   | Solution                                                                                                                            |
| ---------------------------------------------------- | ----------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| **MSBuild error: .NET Framework v3.5 not found**     | Project targets **.NET Framework 3.5** but it wasn’t installed          | Installed **.NET Framework 3.5 Developer Pack** from [https://aka.ms/msbuild/developerpacks](https://aka.ms/msbuild/developerpacks) |
| **Scanner end fails**                                | Build did not succeed                                                   | Fixed compilation errors first, then ran `SonarScanner.MSBuild.exe end`                                                             |
| **.NET Core command works but .NET Framework fails** | Different scanners: `dotnet sonarscanner` vs `SonarScanner.MSBuild.exe` | Used correct scanner for project type                                                                                               |

---

## **6. Notes / Observations**

* OWASP WebGoat is intentionally **vulnerable**; many warnings from SonarQube are expected (SQL injection, unsafe hashing, exception misuse).
* You must fix only **build-blocking errors** to run analysis; warnings can be left as-is.
* Always use the **correct scanner** for your project type.
* After analysis, results can be viewed on `http://localhost:9000` under your project key (`webGoat`).

