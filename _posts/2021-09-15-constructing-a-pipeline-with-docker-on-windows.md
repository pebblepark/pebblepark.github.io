---
title: 윈도우에서 Docker + GitLab + Jenkins + React + Spring boot 를 통해 CI/CD 구성하기 - (1) Docker 환경 구성
category:
  - devops
tags:
  - springboot
  - react
  - docker
---

> 윈도우에서 `Docker` 를 통해 `CI/CD Pipeline` 을 구성해봅니다.

<br>

# WSL 설치

- 윈도우에서 `Linux`를 사용하기 위해서  `wsl(Windows Subsystem for Linux)` 을 설치해줍니다.
- 참고 : [https://www.44bits.io/ko/post/wsl2-install-and-basic-usage](https://www.44bits.io/ko/post/wsl2-install-and-basic-usage)

1. **`Linux`용 `Windows` 하위 시스템 활성화 / `Virtual Machine` 기능 활성화**
    - `Windows PowerShell`을 관리자 권한으로 실행 후 아래 명령어를 실행합니다.
    ```powershell
    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart 
    dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
    ```
2. **재부팅**
3. **`Linux` 커널 업데이트 패키지 다운로드**
    - 아래 링크를 통해 패키지를 다운로드 후 실행해줍니다.
    - [Windows 10에 WSL 설치](https://docs.microsoft.com/ko-kr/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package)
4. **`wsl` 버전 2로 변경**
    - `Window PowerShell` 을 관리자 권한으로 실행 후 아래 명령어를 통해 `wsl` 의 기본 버전을 2로 설정해줍니다.
    ```powershell
    wsl --set-default-version 2
    ```

<br>

# Linux 설치

1. **`Linux` 다운로드**
    - `Microsoft Store` 에서 제공하는 `Linux` 를 다운로드 후 설치합니다.
    - [Ubuntu - Microsoft Store](https://www.microsoft.com/ko-kr/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)
    - 설치 후에 실행하여 우분투 서버 계정의 `ID` / `PW` 를 설정해줍니다.
2. **`Linux` 설치 확인**
    - `Window PowerShell`을 관리자 권한으로 실행 후 아래 명령어를 통해 설치가 완료되었는지 확인해줍니다.
    ```powershell
    wsl -l -v
    ```

    - 우분투가 버전 2로 설치된 것을 확인할 수 있습니다.
    ![WSL 버전]({{site.url}}/assets/images/img/2021-09-15-constructing-a-pipeline-with-docker-on-windows/wsl-version.PNG)

<br>

# Docker Desktop 설치

1. **`Docker Desktop for Windows` 설치**
    - 아래 링크를 통해 `Docker Desktop` 을 설치합니다.
    [Docker Desktop for Windows by Docker | Docker Hub](https://hub.docker.com/editions/community/docker-ce-desktop-windows)
2. **설정 변경**
    - 설치 후 실행하여 설정 > `General` > `Use the WSL2 based engine` 옵션 체크 및 `Resource` > `WSL Integration` 으로 이동하여 자신이 사용중인 `WSL2` 배포판이 맞는지 확인합니다.
    ![Use the WSL2 based engine]({{site.url}}/assets/images/img/2021-09-15-constructing-a-pipeline-with-docker-on-windows/docker-desktop-option1.PNG)
    ![WSL Integration]({{site.url}}/assets/images/img/2021-09-15-constructing-a-pipeline-with-docker-on-windows/docker-desktop-option2.png)
    > 현재 사용중인 WSL 배포판이 체크되어 있지 않다면 체크합니다.
3. **`Docker` 설치 확인**
    - `Ubuntu Shell` 을 통해 `docker` 명령어 사용이 가능한지 확인합니다.
    ![docker ps]({{site.url}}/assets/images/img/2021-09-15-constructing-a-pipeline-with-docker-on-windows/docker-ps.PNG)

<br>

# Docker 환경 구성 완료

해당 작업을 모두 완료했다면 윈도우에서 `Docker`를 사용할 수 있는 모든 준비가 완료되었습니다! 이제 윈도우에서 자유롭게 `Linux` 명령어 사용이 가능하고 `Docker` 도 사용할 수 있습니다. 

다음편에서는 도커를 사용하여  `GitLab` 을 구성해보고 새로운 `Repository` 를 추가하여 소스를 관리하는 방법에 대해 알아보겠습니다.