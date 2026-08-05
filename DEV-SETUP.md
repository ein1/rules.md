# Windows 11 고정 개발환경 구축 지침

> 기준 상태 확인일: 2026-08-06 (Asia/Seoul)  
> 대상: Windows 11 x64, PowerShell 7  
> 목적: 모든 개발 에이전트가 이 PC에 아래의 동일한 개발환경을 한 번 구축하고 더 이상 변경하지 않는다.

## 1. 절대 규칙

1. 이 문서에 적힌 도구, 버전 제약, 설치 방식, 설치 경로를 그대로 사용한다.
2. 현재 PC에 허용 범위 안의 버전이 이미 설치되어 있으면 더 최신 버전이 있어도 변경하지 않는다.
3. 신규 설치 시 현재 검증 버전을 우선한다. 기존 URL이 깨지면 공식 릴리스 보관소에서 같은 버전을 먼저 찾고, 해당 버전 자체를 공식 배포처에서 받을 수 없을 때만 허용 범위 안의 최신 LTS/stable로 승격한다.
4. Beta, Canary, Dev, Nightly, Preview, RC, EAP 버전은 사용하지 않는다.
5. EXE, MSI, MSIX 설치 프로그램으로 설치하는 도구는 설치 프로그램의 기본 경로를 사용한다. 사용자 지정 설치 경로를 사용하지 않는다.
6. 설치 프로그램이 없는 ZIP 또는 단일 실행 파일은 `C:\dev\tools\<도구-실제버전>`에 둔다.
7. WinGet 포터블 패키지는 반드시 문서에 적힌 `--location` 규칙으로 설치한다.
8. `npm install -g`, `npm update -g`, `pnpm add -g`, `pnpm setup`, `npm config set prefix`를 실행하지 않는다.
9. npm 레지스트리의 동명 패키지나 래퍼로 이 문서의 시스템 도구를 대체하지 않는다.
10. Yarn은 설치하지 않는다. npm은 Node.js에 포함된 버전을 사용하며 별도로 설치하거나 승격하지 않는다.
11. 설치 프로그램, WinGet, VS Code Extension Pack 등이 자동 관리하는 의존 항목은 별도로 설치·이동·고정·기록하지 않는다.
12. 문서에 없는 패키지 관리자, 런타임, SDK, CLI, VS Code 확장을 추가하지 않는다.
13. 이미 허용 범위 안의 버전과 올바른 경로가 확인된 항목은 재설치하지 않는다.

### 1.1 버전 제약 해석

| 표기 | 의미 | 허용 범위 예시 |
|---|---|---|
| `8` | 메이저 8 계열 | `>=8.0.0`, `<9.0.0` |
| `8 이상` | 8을 포함한 그 이상 | `>=8.0.0`, 상한 없음 |
| `8.0` | 메이저 8, 마이너 0 계열 | `>=8.0.0`, `<8.1.0` |
| `8.0.36 정확히` | 지정 버전 하나 | `=8.0.36` |

버전 선택 순서는 항상 다음과 같다.

1. 현재 PC에 설치된 버전이 허용 범위 안이면 그대로 사용한다.
2. 미설치 상태이면 표의 현재 검증 버전을 설치한다.
3. 현재 URL이나 manifest가 없어졌으면 공식 릴리스 보관소에서 같은 검증 버전을 먼저 찾는다.
4. 같은 검증 버전도 공식 배포처에서 받을 수 없을 때만 허용 범위 안의 가장 높은 LTS/stable을 선택한다.
5. 승격 버전은 공식 배포처에서만 받는다.
6. 선택한 실제 버전으로 `C:\dev\tools` 폴더명, 시스템 환경 변수, PATH, 이 문서의 검증 버전을 맞춘다.
7. 구축 완료 후에는 허용 범위 안의 새 버전이 나와도 다시 승격하지 않는다.

## 2. 설치 목록과 허용 범위

### 2.1 기본 경로 설치 프로그램

다음 도구는 EXE/MSI/MSIX 설치 방식이며 `--location`을 사용하지 않는다.

| 도구 | 현재 검증 버전 | 허용 범위 | WinGet ID | 확인 경로 |
|---|---:|---|---|---|
| PowerShell | `7.6.4` | `7.6` stable | `Microsoft.PowerShell` | `C:\Program Files\PowerShell\7` |
| Windows Terminal | `1.24.11911.0` | `1.24` stable | `Microsoft.WindowsTerminal` | Windows 앱 기본 경로 |
| Visual Studio Code | `1.132.0` stable | `1.132` stable | `Microsoft.VisualStudioCode` | `C:\Users\myein\AppData\Local\Programs\Microsoft VS Code` |
| Git for Windows | `2.55.0.windows.3` | `2.55` stable | `Git.Git` | `C:\Program Files\Git` |
| GitHub CLI | `2.97.0` | `2.97` stable | `GitHub.cli` | `C:\Program Files\GitHub CLI` |
| 7-Zip | `26.02` | `26.02` stable | `7zip.7zip` | `C:\Program Files\7-Zip` |
| Node.js | `24.19.0` LTS | `24` LTS | `OpenJS.NodeJS.LTS` | `C:\Program Files\nodejs` |
| npm | `11.17.0` | Node.js 선택 결과 | 별도 설치하지 않음 | Node.js에 포함 |
| Microsoft OpenJDK | `25.0.4+7` LTS | `25` LTS | `Microsoft.OpenJDK.25` | `C:\Program Files\Microsoft\jdk-25.0.4.7-hotspot` |
| CMake | `4.4.1` | `4.4` stable | `Kitware.CMake` | `C:\Program Files\CMake` |
| Visual Studio Build Tools 2022 | `17.14.37` (`17.14.37516.0`) RTW | `17.14` stable | `Microsoft.VisualStudio.2022.BuildTools` | `C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools` |
| Windows SDK | `10.0.26100.0` | `10.0.26100` stable | `Microsoft.WindowsSDK.10.0.26100` | `C:\Program Files (x86)\Windows Kits\10` |

### 2.2 WinGet 포터블 도구

| 도구 | 현재 검증 버전 | 허용 범위 | WinGet ID | 현재 설치 위치 |
|---|---:|---|---|---|
| jq | `1.8.2` | `1.8` stable | `jqlang.jq` | `C:\dev\tools\jq-1.8.2` |
| yq | `4.53.3` | `4` stable | `MikeFarah.yq` | `C:\dev\tools\yq-4.53.3` |
| ripgrep | `15.2.0` | `15` stable | `BurntSushi.ripgrep.MSVC` | `C:\dev\tools\ripgrep-15.2.0` |
| fd | `10.4.2` | `10` stable | `sharkdp.fd` | `C:\dev\tools\fd-10.4.2` |
| fzf | `0.74.2` | `0.74` stable | `junegunn.fzf` | `C:\dev\tools\fzf-0.74.2` |
| bat | `0.26.1` | `0.26` stable | `sharkdp.bat` | `C:\dev\tools\bat-0.26.1` |
| zoxide | `0.10.0` | `0.10` stable | `ajeetdsouza.zoxide` | `C:\dev\tools\zoxide-0.10.0` |
| delta | `0.19.2` | `0.19` stable | `dandavison.delta` | `C:\dev\tools\delta-0.19.2` |
| just | `1.58.0` | `1` stable | `Casey.Just` | `C:\dev\tools\just-1.58.0` |
| pnpm | `11.15.1` | `11` stable | `pnpm.pnpm` | `C:\dev\tools\pnpm-11.15.1` |
| uv | `0.12.0` | `0.12` stable | `astral-sh.uv` | `C:\dev\tools\uv-0.12.0` |
| Ninja | `1.13.2` | `1.13` stable | `Ninja-build.Ninja` | `C:\dev\tools\ninja-1.13.2` |
| FFmpeg | `9.0` | `9` stable | `Gyan.FFmpeg` | `C:\dev\tools\ffmpeg-9.0` |

Windows용 FFmpeg는 FFmpeg 공식 다운로드 페이지가 안내하는 gyan.dev의 64-bit static full build를 사용한다.

### 2.3 공식 압축 배포본

| 도구 | 현재 검증 버전 | 허용 범위 | 현재 공식 파일 | 현재 설치 위치 |
|---|---:|---|---|---|
| Python | `3.14.6` stable | `3.14` stable | uv 관리 CPython | `C:\dev\tools\python\cpython-3.14.6-windows-x86_64-none` |
| Apache Maven | `3.9.16` | `3.9` stable | `apache-maven-3.9.16-bin.zip` | `C:\dev\tools\apache-maven-3.9.16` |
| Gradle | `9.6.1` | `9` stable | `gradle-9.6.1-bin.zip` | `C:\dev\tools\gradle-9.6.1` |
| Flutter | `3.44.8` stable | `3.44` stable | `flutter_windows_3.44.8-stable.zip` | `C:\dev\tools\flutter-3.44.8` |
| Android Command-line Tools | `22.0` | `22` stable | `commandlinetools-win-15859902_latest.zip` | `C:\dev\tools\android-sdk\cmdline-tools\latest` |
| PostgreSQL | `17.10` | `17` stable | `postgresql-17.10-1-windows-x64-binaries.zip` | `C:\dev\tools\postgresql-17.10` |
| Railway CLI | `5.30.4` stable | `5` stable | `railway-v5.30.4-x86_64-pc-windows-msvc.zip` | `C:\dev\tools\railway-5.30.4` |

### 2.4 Android SDK 고정 구성

| 구성요소 | 현재 검증 버전 | 허용 범위 |
|---|---:|---|
| SDK Platform | API `36`, revision `2` | API `36`의 최신 stable revision |
| Build Tools | `36.0.0` | `36.0` stable |
| Platform Tools | `37.0.1` | `37` stable |
| Emulator | `37.1.11` | `37` stable |
| Google APIs x86_64 시스템 이미지 | API `36`, revision `7` | 같은 API·태그·ABI의 최신 stable revision |

Android SDK 라이선스는 모두 승인한다.

## 3. 설치 실행

모든 명령은 PowerShell 7에서 실행한다. 시스템 환경 변수와 시스템 PATH를 설정하는 단계는 관리자 권한으로 실행한다.

### 3.1 기본 경로 설치 프로그램

다음은 현재 검증 버전의 설치 명령이다. 해당 WinGet manifest가 없어졌을 때만 2.1절의 허용 범위 안에서 가장 높은 LTS/stable 버전으로 `--version` 값을 바꾼다. 설치 위치 인수는 추가하지 않는다.

```powershell
winget install --id Microsoft.PowerShell -e --version 7.6.4.0 --source winget --accept-package-agreements --accept-source-agreements
winget install --id Microsoft.WindowsTerminal -e --version 1.24.11911.0 --source winget --accept-package-agreements --accept-source-agreements
winget install --id Microsoft.VisualStudioCode -e --version 1.132.0 --source winget --accept-package-agreements --accept-source-agreements
winget install --id Git.Git -e --version 2.55.0.3 --source winget --accept-package-agreements --accept-source-agreements
winget install --id GitHub.cli -e --version 2.97.0 --source winget --accept-package-agreements --accept-source-agreements
winget install --id 7zip.7zip -e --version 26.02 --source winget --accept-package-agreements --accept-source-agreements
winget install --id OpenJS.NodeJS.LTS -e --version 24.19.0 --source winget --accept-package-agreements --accept-source-agreements
winget install --id Microsoft.OpenJDK.25 -e --version 25.0.4.7 --source winget --accept-package-agreements --accept-source-agreements
winget install --id Kitware.CMake -e --version 4.4.1 --source winget --accept-package-agreements --accept-source-agreements
winget install --id Microsoft.WindowsSDK.10.0.26100 -e --version 10.0.26100.7705 --source winget --accept-package-agreements --accept-source-agreements
```

Visual Studio Build Tools는 다음 명령만 사용한다.

```powershell
winget install --id Microsoft.VisualStudio.2022.BuildTools -e --version 17.14.37 --source winget `
    --accept-package-agreements --accept-source-agreements `
    --override "--wait --passive --norestart --add Microsoft.VisualStudio.Workload.VCTools --includeRecommended"
```

### 3.2 WinGet 포터블 도구

다음은 현재 검증 버전의 설치 명령이다. 해당 WinGet manifest가 없어졌을 때만 2.2절의 허용 범위 안에서 가장 높은 stable을 선택하고, `--version`과 `--location`의 버전 부분을 선택한 실제 버전으로 함께 바꾼다.

```powershell
winget install --id jqlang.jq -e --version 1.8.2 --source winget --location 'C:\dev\tools\jq-1.8.2' --accept-package-agreements --accept-source-agreements
winget install --id MikeFarah.yq -e --version 4.53.3 --source winget --location 'C:\dev\tools\yq-4.53.3' --accept-package-agreements --accept-source-agreements
winget install --id BurntSushi.ripgrep.MSVC -e --version 15.2.0 --source winget --location 'C:\dev\tools\ripgrep-15.2.0' --accept-package-agreements --accept-source-agreements
winget install --id sharkdp.fd -e --version 10.4.2 --source winget --location 'C:\dev\tools\fd-10.4.2' --accept-package-agreements --accept-source-agreements
winget install --id junegunn.fzf -e --version 0.74.2 --source winget --location 'C:\dev\tools\fzf-0.74.2' --accept-package-agreements --accept-source-agreements
winget install --id sharkdp.bat -e --version 0.26.1 --source winget --location 'C:\dev\tools\bat-0.26.1' --accept-package-agreements --accept-source-agreements
winget install --id ajeetdsouza.zoxide -e --version 0.10.0 --source winget --location 'C:\dev\tools\zoxide-0.10.0' --accept-package-agreements --accept-source-agreements
winget install --id dandavison.delta -e --version 0.19.2 --source winget --location 'C:\dev\tools\delta-0.19.2' --accept-package-agreements --accept-source-agreements
winget install --id Casey.Just -e --version 1.58.0 --source winget --location 'C:\dev\tools\just-1.58.0' --accept-package-agreements --accept-source-agreements
winget install --id pnpm.pnpm -e --version 11.15.1 --source winget --location 'C:\dev\tools\pnpm-11.15.1' --accept-package-agreements --accept-source-agreements
winget install --id astral-sh.uv -e --version 0.12.0 --source winget --location 'C:\dev\tools\uv-0.12.0' --accept-package-agreements --accept-source-agreements
winget install --id Ninja-build.Ninja -e --version 1.13.2 --source winget --location 'C:\dev\tools\ninja-1.13.2' --accept-package-agreements --accept-source-agreements
winget install --id Gyan.FFmpeg -e --version 9.0 --source winget --location 'C:\dev\tools\ffmpeg-9.0' --accept-package-agreements --accept-source-agreements
```

### 3.3 압축 배포본

다음 URL은 현재 PC에서 사용한 공식 배포 파일이다. URL을 영구 주소로 가정하지 않는다.

| 도구 | 현재 공식 URL |
|---|---|
| Maven | `https://archive.apache.org/dist/maven/maven-3/3.9.16/binaries/apache-maven-3.9.16-bin.zip` |
| Gradle | `https://services.gradle.org/distributions/gradle-9.6.1-bin.zip` |
| Flutter | `https://storage.googleapis.com/flutter_infra_release/releases/stable/windows/flutter_windows_3.44.8-stable.zip` |
| Android CLI | `https://dl.google.com/android/repository/commandlinetools-win-15859902_latest.zip` |
| PostgreSQL | `https://get.enterprisedb.com/postgresql/postgresql-17.10-1-windows-x64-binaries.zip` |
| Railway CLI | `https://github.com/railwayapp/cli/releases/download/v5.30.4/railway-v5.30.4-x86_64-pc-windows-msvc.zip` |

현재 검증 URL에서 파일을 받을 수 없을 때는 다음 공식 릴리스 목록만 사용한다.

| 도구 | 공식 릴리스 목록 |
|---|---|
| Maven | `https://maven.apache.org/download.cgi` |
| Gradle | `https://gradle.org/releases/` |
| Flutter | `https://docs.flutter.dev/install/archive` |
| Android Command-line Tools | `https://developer.android.com/studio#command-tools` |
| PostgreSQL Windows binaries | `https://www.enterprisedb.com/download-postgresql-binaries` |
| Railway CLI | `https://github.com/railwayapp/cli/releases` |

링크 소멸 시 다음 순서로 처리한다.

1. 공식 릴리스 목록에서 현재 검증 버전의 Windows x64 파일을 먼저 찾는다.
2. 같은 버전도 공식 배포처에 없을 때만 2.3절의 허용 범위 안에서 가장 높은 Windows x64용 stable을 선택한다.
3. 선택한 파일은 공식 배포처에서만 받는다.
4. 압축 해제 폴더는 `C:\dev\tools\<도구-선택버전>`으로 만든다. Android SDK만 고정 경로 `C:\dev\tools\android-sdk`를 사용한다.
5. 선택한 실제 버전에 맞게 시스템 환경 변수와 PATH를 변경한다.
6. 6절 검증을 완료한 뒤 이 문서의 현재 검증 버전, 파일명, URL, 설치 경로를 실제 값으로 교체한다.
7. 비공식 미러, npm 래퍼, 임의 재패키징 파일은 사용하지 않는다.

현재 검증 버전의 압축 해제 결과는 다음 형태다. 보수적 승격을 사용했다면 경로의 버전 부분만 선택한 실제 버전으로 바꾼다.

```text
C:\dev\tools\apache-maven-3.9.16\bin\mvn.cmd
C:\dev\tools\gradle-9.6.1\bin\gradle.bat
C:\dev\tools\flutter-3.44.8\bin\flutter.bat
C:\dev\tools\android-sdk\cmdline-tools\latest\bin\sdkmanager.bat
C:\dev\tools\postgresql-17.10\bin\psql.exe
C:\dev\tools\railway-5.30.4\railway.exe
```

### 3.4 Python과 Android SDK

다음은 현재 검증 버전의 명령이다. Python 3.14.6을 uv가 더 이상 제공하지 않을 때만 `3.14` 계열의 최신 stable 패치 버전으로 숫자를 바꾸고 실제 설치 경로를 문서에 반영한다. Android 구성요소는 `--channel=0`의 stable만 사용하며 2.4절의 허용 범위를 지킨다.

```powershell
$env:UV_PYTHON_INSTALL_DIR = 'C:\dev\tools\python'
uv python install 3.14.6 --default

$sdkManager = 'C:\dev\tools\android-sdk\cmdline-tools\latest\bin\sdkmanager.bat'
1..200 | ForEach-Object { 'y' } | & $sdkManager --licenses --sdk_root='C:\dev\tools\android-sdk'
& $sdkManager --channel=0 --sdk_root='C:\dev\tools\android-sdk' `
    'platform-tools' `
    'platforms;android-36' `
    'build-tools;36.0.0' `
    'emulator' `
    'system-images;android-36;google_apis;x86_64'

flutter config --android-sdk 'C:\dev\tools\android-sdk'
```

## 4. 시스템 환경 변수와 PATH

### 4.1 시스템 환경 변수

다음 값을 시스템(Machine) 환경 변수로 등록한다.

| 변수 | 고정 값 |
|---|---|
| `DEVTOOLS_HOME` | `C:\dev\tools` |
| `JAVA_HOME` | `C:\Program Files\Microsoft\jdk-25.0.4.7-hotspot` |
| `MAVEN_HOME` | `C:\dev\tools\apache-maven-3.9.16` |
| `M2_HOME` | `C:\dev\tools\apache-maven-3.9.16` |
| `GRADLE_HOME` | `C:\dev\tools\gradle-9.6.1` |
| `FLUTTER_HOME` | `C:\dev\tools\flutter-3.44.8` |
| `ANDROID_HOME` | `C:\dev\tools\android-sdk` |
| `ANDROID_SDK_ROOT` | `C:\dev\tools\android-sdk` |
| `PNPM_HOME` | `C:\dev\tools\pnpm-11.15.1` |
| `UV_INSTALL_DIR` | `C:\dev\tools\uv-0.12.0` |
| `UV_PYTHON_INSTALL_DIR` | `C:\dev\tools\python` |

`POSTGRESQL_HOME`, `RAILWAY_HOME`, `PGDATA`, `NPM_HOME`은 등록하지 않는다.

### 4.2 시스템 PATH

설치 프로그램이 등록한 기본 경로를 유지하고, 다음 항목이 시스템 PATH에 정확히 한 번씩 존재하게 한다.

```text
C:\Program Files\PowerShell\7
C:\Program Files\Git\cmd
C:\Program Files\GitHub CLI
C:\Program Files\nodejs
C:\Program Files\Microsoft\jdk-25.0.4.7-hotspot\bin
C:\Program Files\CMake\bin
C:\Program Files\7-Zip
C:\Users\myein\AppData\Local\Programs\Microsoft VS Code\bin
C:\dev\tools\jq-1.8.2
C:\dev\tools\yq-4.53.3
C:\dev\tools\ripgrep-15.2.0\ripgrep-15.2.0-x86_64-pc-windows-msvc
C:\dev\tools\fd-10.4.2\fd-v10.4.2-x86_64-pc-windows-msvc
C:\dev\tools\fzf-0.74.2
C:\dev\tools\bat-0.26.1\bat-v0.26.1-x86_64-pc-windows-msvc
C:\dev\tools\zoxide-0.10.0
C:\dev\tools\delta-0.19.2\delta-0.19.2-x86_64-pc-windows-msvc
C:\dev\tools\just-1.58.0
C:\dev\tools\pnpm-11.15.1
C:\dev\tools\uv-0.12.0
C:\dev\tools\ninja-1.13.2
C:\dev\tools\ffmpeg-9.0\ffmpeg-9.0-full_build\bin
C:\dev\tools\python\cpython-3.14.6-windows-x86_64-none
C:\dev\tools\apache-maven-3.9.16\bin
C:\dev\tools\gradle-9.6.1\bin
C:\dev\tools\flutter-3.44.8\bin
C:\dev\tools\android-sdk\cmdline-tools\latest\bin
C:\dev\tools\android-sdk\platform-tools
C:\dev\tools\android-sdk\emulator
C:\dev\tools\postgresql-17.10\bin
C:\dev\tools\railway-5.30.4
```

환경 변수와 PATH를 설정한 뒤 열려 있던 터미널과 VS Code를 모두 종료하고 다시 연다.

## 5. VS Code 명시 확장

다음 확장만 명시 설치한다. 이 목록에 없는 자동 의존 확장은 기록하거나 별도로 설치하지 않는다.

| 확장 ID | 현재 검증 버전 |
|---|---:|
| `ms-vscode.powershell` | `2025.4.0` |
| `dbaeumer.vscode-eslint` | `3.0.34` |
| `esbenp.prettier-vscode` | `12.4.0` |
| `ms-python.python` | `2026.4.0` |
| `charliermarsh.ruff` | `2026.68.0` |
| `ms-toolsai.jupyter` | `2025.9.1` |
| `vscjava.vscode-java-pack` | `0.31.1` |
| `dart-code.dart-code` | `3.140.0` |
| `dart-code.flutter` | `3.140.0` |
| `ms-vscode.cpptools` | `1.32.2` |
| `ms-vscode.cmake-tools` | `1.23.52` |
| `redhat.vscode-yaml` | `1.24.0` |
| `tamasfe.even-better-toml` | `0.21.2` |
| `ms-azuretools.vscode-docker` | `2.0.0` |
| `ms-vscode-remote.remote-containers` | `0.466.0` |
| `github.vscode-github-actions` | `0.32.3` |
| `editorconfig.editorconfig` | `0.18.2` |
| `usernamehw.errorlens` | `3.28.0` |
| `streetsidesoftware.code-spell-checker` | `4.5.6` |
| `eamodio.gitlens` | `18.3.0` |
| `gruntfuggly.todo-tree` | `0.0.226` |
| `humao.rest-client` | `0.25.1` |

설치 형식은 다음과 같다.

```powershell
code --install-extension <확장ID>@<선택버전> --force
```

표의 현재 검증 버전을 Marketplace에서 더 이상 받을 수 없을 때만 동일한 메이저·마이너 계열의 최신 stable 패치 버전을 설치한다. Pre-Release는 사용하지 않으며 선택한 실제 버전을 표에 기록한다.

## 6. 최종 검증

새 PowerShell 7에서 다음 명령을 실행한다.

```powershell
pwsh --version
code --version
git --version
git lfs version
gh --version
jq --version
yq --version
rg --version
fd --version
fzf --version
bat --version
zoxide --version
delta --version
just --version
node --version
npm --version
pnpm --version
uv --version
python --version
java --version
javac --version
mvn --version
gradle --version
flutter --version
adb version
cmake --version
ninja --version
ffmpeg -version
ffprobe -version
ffplay -version
psql --version
postgres --version
pg_ctl --version
initdb --version
railway --version
flutter doctor -v
code --list-extensions --show-versions
```

현재 PC의 필수 기대값:

```text
PowerShell 7.6.4
VS Code 1.132.0
Git 2.55.0.windows.3
GitHub CLI 2.97.0
Node.js 24.19.0
npm 11.17.0
pnpm 11.15.1
Python 3.14.6
uv 0.12.0
OpenJDK/javac 25.0.4 LTS
Maven 3.9.16
Gradle 9.6.1
Flutter 3.44.8 stable
CMake 4.4.1
Ninja 1.13.2
FFmpeg 9.0
PostgreSQL 17.10
Railway CLI 5.30.4
flutter doctor: No issues found!
```

보수적 승격 절차를 사용한 신규 구축에서는 위 숫자 대신 갱신된 2절과 5절의 실제 선택 버전이 기대값이다.

각 명령의 버전과 실행 경로가 이 문서와 일치하고 `flutter doctor -v`가 문제 없이 끝나면 구축 완료다. 이후 에이전트는 이 PC의 개발환경을 추가 설치·재설치·승격·대체하지 않는다.
