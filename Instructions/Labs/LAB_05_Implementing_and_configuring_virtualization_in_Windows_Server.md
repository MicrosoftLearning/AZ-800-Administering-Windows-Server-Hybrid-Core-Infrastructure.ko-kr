---
lab:
  title: '랩: Windows Server에서 가상화 구현 및 구성'
  module: 'Module 5: Hyper-V virtualization in Windows Server'
ms.openlocfilehash: c9ff5dddf134be5073ec9f2fa33d84ca07b0a343
ms.sourcegitcommit: bd43c7961e93ef200b92fb1d6f09d9ad153dd082
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2022
ms.locfileid: "137907004"
---
# <a name="lab-implementing-and-configuring-virtualization-in-windows-server"></a>랩: Windows Server에서 가상화 구현 및 구성

## <a name="scenario"></a>시나리오

Contoso는 미국 시애틀에 본사가 있는 글로벌 엔지니어링 및 제조 회사입니다. IT 사무실과 데이터 센터는 시애틀에서 시애틀 위치 및 기타 위치를 지원합니다. Contoso는 최근 Windows Server와 클라이언트 인프라를 배포했습니다. 

현재 많은 물리적 서버의 사용율이 낮아, 회사는 가상화를 확장하여 환경을 최적화하려 합니다. 그래서 Hyper-V를 사용하여 가상 머신 환경을 관리하는 방법을 확인하기 위해 개념 증명을 수행하기로 합니다. 또한 Contoso DevOps 팀은 새 애플리케이션의 배포 시간을 줄이고 애플리케이션을 클라우드로 간편하게 옮길 수 있는지 확인하기 위해 컨테이너 기술을 탐색하려 합니다. 여러분은 팀과 협력하여 Windows Server 컨테이너를 평가하고, 컨테이너에 인터넷 정보 서비스(웹 서비스)를 제공하려 합니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음을 수행할 수 있습니다.

- VM을 생성하고 구성합니다.
- 컨테이너를 설치하고 구성합니다.

## <a name="estimated-time-60-minutes"></a>예상 시간: 60분

## <a name="lab-setup"></a>랩 설정

가상 머신: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1** 과 **AZ-800T00A-ADM1** 을 실행해야 합니다. 다른 VM을 실행할 수도 있지만 이 랩에서는 필요하지 않습니다.

> **참고**: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1** 및 **AZ-800T00A-SEA-ADM1** 가상 머신은 **SEA-DC1**, **SEA-SVR1** 및 **SEA-ADM1** 의 설치를 호스트합니다.

1. **SEA-ADM1** 을 선택합니다.
1. 다음 자격 증명을 사용하여 로그인합니다.

   - 사용자 이름: **Administrator**
   - 암호: **Pa55w.rd**
   - 도메인: **CONTOSO**

이 랩에서는 사용 가능한 VM 환경을 사용합니다.

## <a name="exercise-1-creating-and-configuring-vms"></a>연습 1: VM 만들기 및 구성

### <a name="scenario"></a>시나리오

이 연습에서는 Hyper-V 관리자와 Windows Admin Center를 사용하여 가상 머신을 만들고 구성합니다. 먼저 프라이빗 가상 네트워크 스위치를 만듭니다. 그런 다음 VM에 설치할 운영 체제를 이용해 이미 준비된 기본 이미지의 차이점 보관용 드라이브를 만듭니다. 마지막으로, 개념 증명을 위해 준비한 차이점 보관용 드라이브와 프라이빗 스위치를 사용하는 1세대 VM을 만듭니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Hyper-V 가상 스위치 만들기
1. 가상 하드 디스크 만들기
1. 가상 머신 만들기
1. Windows Admin Center를 사용하여 가상 머신 관리

#### <a name="task-1-create-a-hyper-v-virtual-switch"></a>작업 1: Hyper-V 가상 스위치 만들기

1. **SEA-ADM1** 에서 **서버 관리자** 를 엽니다. 
1. 서버 관리자에서 **모든 서버** 를 선택합니다. 
1. 서버 목록에서 **SEA-SVR1** 을 선택하고 상황에 맞는 메뉴를 사용하여 해당 서버를 대상으로 하는 **Hyper-V 관리자** 를 시작합니다. 
1. **Hyper-V 관리자** 에서 **가상 스위치 관리자** 를 사용하여, **SEA-SVR1** 에서 다음 설정을 바탕으로 가상 스위치를 만듭니다.

   - 이름: **Contoso Private Switch**
   - 연결 형식: **프라이빗 네트워크**

#### <a name="task-2-create-a-virtual-hard-disk"></a>작업 2: 가상 하드 디스크 만들기

1. **SEA-ADM1** 의 Hyper-V 관리자에서 **새 가상 하드 디스크 마법사** 를 사용하여, **SEA-SVR1** 에서 다음 설정을 바탕으로 가상 스위치를 만듭니다. 

   - 디스크 형식: **VHD**
   - 디스크 유형: **차이점 보관용**
   - 이름: **SEA-VM1**
   - 위치: **C:\Base**
   - 부모 디스크: **C:\Base\BaseImage.vhd**

#### <a name="task-3-create-a-virtual-machine"></a>작업 3: 가상 머신 만들기

1. **SEA-ADM1** 에서 Hyper-V 관리자의 **SEA-SVR1** 에서 다음 설정을 사용하여 새 가상 머신을 만듭니다. 

   - 이름: **SEA-VM1**
   - 위치: **C:\Base**
   - 세대: **1세대**
   - 메모리: **4096**
   - 네트워킹: **Contoso Private Switch**
   - 하드 디스크: **C:\Base\SEA-VM1.vhd**

1. **SEA-VM1** 의 **설정** 창을 열고 최대 RAM 값을 **4096** 으로 하여 **동적 메모리** 를 사용하도록 설정합니다.
1. Hyper-V 관리자를 닫습니다.

#### <a name="task-4-manage-virtual-machines-using-windows-admin-center"></a>작업 4: Windows Admin Center를 사용하여 가상 머신 관리

1. **SEA-ADM1** 에서 관리자 권한으로 **Windows PowerShell** 을 시작합니다.

   >**참고**: **SEA-ADM1** 에 Windows Admin Center를 아직 설치하지 않았다면 다음 두 단계를 수행합니다.

1. **Windows PowerShell** 콘솔에서 다음 명령을 실행한 다음 Enter 키를 눌러 Windows Admin Center 최신 버전을 다운로드합니다.
    
   ```powershell
   Start-BitsTransfer -Source https://aka.ms/WACDownload -Destination "$env:USERPROFILE\Downloads\WindowsAdminCenter.msi"
   ```
1. 다음 명령을 입력한 다음 Enter 키를 눌러 Windows Admin Center를 설치합니다.
    
   ```powershell
   Start-Process msiexec.exe -Wait -ArgumentList "/i $env:USERPROFILE\Downloads\WindowsAdminCenter.msi /qn /L*v log.txt REGISTRY_REDIRECT_PORT_80=1 SME_PORT=443 SSL_CERTIFICATE_OPTION=generate"
   ```

   > **참고**: 설치가 완료될 때까지 기다리세요. 이 작업은 2분 정도 걸립니다.

1. **SEA-ADM1** 에서 Microsoft Edge 시작하고 `https://SEA-ADM1.contoso.com`에 있는 Windows Admin Center의 로컬 인스턴스에 연결합니다. 
1. 메시지가 표시되면 **Windows 보안** 대화 상자에 다음 자격 증명을 입력한 다음 **확인** 을 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. Windows Admin Center에서 **sea-svr1.contoso.com** 에 연결을 추가하고 **Pa55w.rd** 암호를 사용하여 **CONTOSO\\Administrator** 자격으로 연결합니다. 
1. **도구** 목록에서 **가상 머신** 을 선택하고 **요약** 창을 검토합니다.
1. Windows Admin Center를 사용하여 5GB 크기의 새 디스크를 만듭니다.
1. Windows Admin Center를 사용하여 **SEA-VM1** 을 시작한 다음 실행 중인 VM 관련 통계를 표시합니다.
1. Windows Admin Center를 사용하여 **SEA-VM1** 을 종료합니다.
1. **도구** 목록에서 **가상 스위치** 를 선택하고 기존 스위치를 식별합니다.

### <a name="exercise-1-results"></a>연습 1 결과

이 연습이 끝나면 Hyper-V 관리자 및 Windows Admin Center를 사용하여 가상 스위치, 가상 하드 디스크, 가상 머신을 만든 다음 가상 머신을 관리하게 됩니다.

## <a name="exercise-2-installing-and-configuring-containers"></a>연습 2: 컨테이너 설치 및 구성

### <a name="scenario"></a>시나리오

이 연습에서는 Docker를 사용하여 Windows 컨테이너를 설치하고 실행합니다. 또한 Windows Admin Center를 사용하여 컨테이너를 관리합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Windows Server에 Docker 설치
1. Windows 컨테이너 설치 및 실행
1. Windows Admin Center를 사용하여 컨테이너 관리

#### <a name="task-1-install-docker-on-windows-server"></a>작업 1: Windows Server에 Docker 설치

1. **SEA-ADM1** 의 Windows Admin Center에서, **sea-svr1.contoso.com** 연결된 상태에서 **도구** 메뉴를 사용하여 해당 서버에 대한 PowerShell 원격 작업 세션을 설정합니다. 

   > **참고**: Windows Admin Center에서의 Powershell 연결은 랩에서 사용하는 중첩된 가상화 때문에 상대적으로 느릴 수 있습니다. 이 경우 **SEA-ADM1** 의 Windows Powershell 콘솔에서 `Enter-PSSession -ComputerName SEA-SVR1`을 실행하세요.

1. PowerShell 콘솔에서 다음 명령을 실행하여 TLS 1.2를 강제로 사용하고 PowerShellGet 모듈을 설치합니다. 

   ```powershell
   [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12
   Install-PackageProvider -Name NuGet -Force
   Install-Module PowerShellGet -RequiredVersion 2.2.4 -SkipPublisherCheck
   ```
1. 설치가 완료되면 다음 명령을 실행하여 **SEA-SVR1** 을 다시 시작합니다.

   ```powershell
   Restart-Computer -Force
   ```
1. **SEA-SVR1** 이 다시 시작되면 **PowerShell** 도구를 다시 사용하여 **SEA-SVR1** 에 대한 새 PowerShell 원격 세션을 설정합니다.

1. **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 **SEA-SVR1** 에 Docker Microsoft PackageManagement 공급자를 설치합니다.

   ```powershell
   Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
   ```
1. **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 **SEA-SVR1** 에 Docker 런타임을 설치합니다.

   ```powershell
   Install-Package -Name docker -ProviderName DockerMsftProvider
   ```
1. 설치가 완료되면 다음 명령을 실행하여 **SEA-SVR1** 을 다시 시작합니다.

   ```powershell
   Restart-Computer -Force
   ```

#### <a name="task-2-install-and-run-a-windows-container"></a>작업 2: Windows 컨테이너 설치 및 실행

1. **SEA-SVR1** 이 다시 시작되면 **PowerShell** 도구를 다시 사용하여 **SEA-SVR1** 에 대한 새 PowerShell 원격 세션을 설정합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 설치된 Docker버전을 확인합니다.

   ```powershell
   Get-Package -Name Docker -ProviderName DockerMsftProvider
   ```
1. 다음 명령을 실행하여 현재 **SEA-SVR1** 에 존재하는 Docker 이미지를 식별합니다. 

   ```powershell
   docker images
   ```

   > **참고**: 로컬 리포지토리 저장소에 이미지가 없는지 확인합니다.

1. 다음 명령을 실행하여 온라인 Microsoft 리포지토리의 Docker 기본 이미지를 나열합니다.

   ```powershell
   docker search Microsoft
   ```
1. 다음 명령을 실행하여 IIS(인터넷 정보 서비스) 설치가 포함된 Nano 서버 이미지를 다운로드합니다.

   ```powershell
   docker pull nanoserver/iis
   ```

   > **참고**: 다운로드를 완료하는 데 걸리는 시간은 랩 VM에서 Microsoft 컨테이너 레지스트리로 이어지는 네트워크 연결의 가용 대역폭에 따라 달라집니다.

1. 다음 명령을 실행하여 Docker 이미지가 성공적으로 다운로드되었는지 확인합니다.

   ```powershell
   docker images
   ```
1. 다음 명령을 실행하여 다운로드한 이미지를 기준으로 컨테이너를 시작합니다.

   ```powershell
   docker run --isolation=hyperv -d -t --name nano -p 80:80 nanoserver/iis 
   ```

   > **참고**: Docker 명령은 컨테이너를 모든 호스트 운영 체제 비호환성 문제를 해결하는 Hyper-V 격리 모드에서 백그라운드 서비스(`-d`)로 시작하고, 컨테이너 호스트의 포트 80이 컨테이너의 포트 80에 매핑되도록 네트워킹을 구성합니다. 

1. 다음 명령을 실행하여 컨테이너 호스트의 IP 주소 정보를 검색합니다.

   ```powershell
   ipconfig
   ```

   > **참고**: vEthernet(nat)이라는 이더넷 어댑터의 IPv4 주소를 식별합니다. 이것은 새 컨테이너의 주소입니다. 그런 다음 **Ethernet** 이라는 이더넷 어댑터의 IPv4 주소를 식별합니다. 이것은 호스트(**SEA-SVR1**)의 IP 주소이며 **172.16.10.12** 로 설정됩니다.

1. **SEA-ADM1** 에서 Microsoft Edge 창으로 전환하고 다른 탭을 연 다음 **http://172.16.10.12** 로 이동합니다. 브라우저에서 기본 IIS 페이지가 표시되는지 확인합니다.
1. **SEA-ADM1** 에서 PowerShell 원격 세션을 다시 **SEA-SVR1** 로 전환하고, **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 실행 중인 컨테이너를 나열합니다.

   ```powershell
   docker ps
   ```
   > **참고**: 이 명령은 현재 **SEA-SVR1** 에서 실행 중인 컨테이너에 관한 정보를 제공합니다. 컨테이너 ID를 기록해 두세요. 컨테이너를 중지하는 용도로 사용해야 합니다. 

1. 다음 명령을 실행하여 실행 중인 컨테이너를 중지합니다(`<ContainerID>` 자리 표시자를 이전 단계에서 식별한 컨테이너 ID로 대체합니다).

   ```powershell
   docker stop <ContainerID>
   ```
1. 다음 명령을 실행하여 컨테이너가 중지되었는지 확인합니다.

   ```powershell
   docker ps
   ```

#### <a name="task-3-use-windows-admin-center-to-manage-containers"></a>작업 3: Windows Admin Center를 사용하여 컨테이너 관리

1. **SEA-ADM1** 의 Windows Admin Center에 있는 **sea-svr1.contoso.com** 의 **도구** 메뉴에서 **컨테이너** 를 선택합니다. **PowerShell** 세션을 닫기 여부를 묻는 메시지가 표시되면 **계속** 을 선택합니다.
1. 컨테이너 창에서 **요약**, **컨테이너**, **이미지**, **네트워크** 및 **볼륨** 탭을 탐색합니다.

### <a name="exercise-2-results"></a>연습 2 결과

이 연습이 끝나면 Windows Server에 Docker를 설치하고, 웹 서비스가 포함된 Windows 컨테이너 이미지를 다운로드하고, 관련 기능을 확인하게 됩니다.