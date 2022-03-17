---
lab:
  title: '랩: Windows Server에서 가상화 구현 및 구성'
  type: Answer Key
  module: 'Module 5: Hyper-V virtualization in Windows Server'
ms.openlocfilehash: 9202bf8b30582e0c3d8aebfddf7ca0762f53024a
ms.sourcegitcommit: bd43c7961e93ef200b92fb1d6f09d9ad153dd082
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2022
ms.locfileid: "137906982"
---
# <a name="lab-answer-key-implementing-and-configuring-virtualization-in-windows-server"></a>랩 응답 키: Windows Server에서 가상화 구현 및 구성

### <a name="exercise-1-creating-and-configuring-vms"></a>연습 1: VM 만들기 및 구성

#### <a name="task-1-create-a-hyper-v-virtual-switch"></a>작업 1: Hyper-V 가상 스위치 만들기

1. **SEA-ADM1** 에 연결하고, 필요하면 **Pa55w.rd** 암호를 사용해 **Contoso\\Administrator** 로 로그인합니다.
1. **SEA-ADM1** 에서 **시작을** 선택한 다음 **서버 관리자** 를 선택합니다.
1. 서버 관리자에서 **모든 서버** 를 선택합니다.
1. 서버 목록에서 **SEA-SVR1** 항목을 선택하고 상황에 맞는 메뉴를 표시한 다음 **Hyper-V 관리자** 를 선택합니다.
1. Hyper-V 관리자에서 **SEA-SVR1.Contoso.com** 이 입력된 선택 상자인지 확인합니다.
1. 작업 창에서 **가상 스위치 관리자** 를 선택합니다.
1. **가상 스위치 관리자** 의 **가상 스위치 만들기** 창에서 **프라이빗** 을 선택한 다음 **가상 스위치 만들기** 를 선택합니다.
1. **가상 스위치 속성** 상자에서 다음 설정을 지정한 다음 **확인** 을 선택합니다.

   - 이름: **Contoso Private Switch**
   - 연결 형식: **프라이빗 네트워크**

#### <a name="task-2-create-a-virtual-hard-disk"></a>작업 2: 가상 하드 디스크 만들기

1. **SEA-ADM1** 의 **SEA-SVR1** 에 연결된 Hyper-V 관리자에서 **새로 만들기** 를 선택한 다음 **하드 디스크** 를 선택합니다. **새 가상 하드 디스크 마법사** 가 시작됩니다.
1. **시작하기 전에** 페이지에서 **다음** 을 선택합니다.
1. **디스크 형식 선택** 페이지에서 **VHD** 를 선택한 후 **다음** 을 선택합니다.
1. **디스크 유형 선택** 페이지에서 **차이점 보관용** 을 선택한 후 **다음** 을 선택합니다.
1. **이름 및 위치 지정** 페이지에서 다음 설정을 지정한 후 **다음** 을 선택합니다.

   - 이름: **SEA-VM1**
   - 위치: **C:\Base**

1. **디스크 구성** 페이지의 **위치** 상자에 **C:\Base\BaseImage.vhd** 를 입력한 후 **다음** 을 선택합니다.
1. **요약** 페이지에서 **마침** 을 선택합니다.

#### <a name="task-3-create-a-virtual-machine"></a>작업 3: 가상 머신 만들기

1. **SEA-ADM1** 의 Hyper-V 관리자에서 **새로 만들기** 를 선택한 다음 **가상 머신** 을 선택합니다. **새 가상 머신 마법사** 가 시작됩니다.
1. **시작하기 전에** 페이지에서 **다음** 을 선택합니다.
1. **이름 및 위치 지정** 페이지에서 **SEA-VM1** 을 입력한 다음 **가상 머신을 다른 위치에 저장** 옆의 확인란을 선택합니다.
1. **위치** 상자에 **C:\Base** 를 입력한 후 **다음** 을 선택합니다.
1. **세대 지정** 페이지에서 **1세대** 를 선택한 후 **다음** 을 선택합니다.
1. **메모리 할당** 페이지에서 **4096** 을 입력한 후 **다음** 을 선택합니다.
1. **네트워킹 구성** 페이지에서 연결 드롭다운 메뉴를 선택하고 **Contoso Private Switch** 를 선택한 후 **다음** 을 선택합니다.
1. **가상 하드 디스크 연결** 페이지에서 **기존 가상 하드 디스크 사용** 을 선택한 다음 **찾아보기** 를 선택합니다.
1. **C:\Base** 로 이동하여 **SEA-VM1.vhd** 를 선택하고 **열기** 를 선택한 후 **다음** 을 선택합니다.
1. **요약** 페이지에서 **마침** 을 선택합니다. **SEA-VM1** 이 가상 머신 목록에 표시됩니다.
1. **SEA-VM1** 을 선택한 다음 작업 창의 **SEA-VM1** 아래에서 **설정** 을 선택합니다.
1. **하드웨어** 목록에서 **메모리** 를 선택합니다.
1. **동적 메모리** 섹션에서 **동적 메모리 사용** 옆의 확인란을 선택합니다.
1. **최대 RAM** 옆에 **4096** 을 입력한 다음 **확인** 을 선택합니다.
1. Hyper-V 관리자를 닫습니다.

#### <a name="task-4-manage-virtual-machines-using-windows-admin-center"></a>작업 4: Windows Admin Center를 사용하여 가상 머신 관리

1. **SEA-ADM1** 에서 **시작** 을 선택한 다음 **Windows PowerShell(관리자)** 을 선택합니다.

   >**참고**: **SEA-ADM1** 에 Windows Admin Center를 아직 설치하지 않았다면 다음 두 단계를 수행합니다.

1. **Windows PowerShell** 콘솔에서 다음 명령을 입력합니다. 그런 다음 Enter 키를 눌러 최신 버전의 Windows Admin Center를 다운로드합니다.
    
   ```powershell
   Start-BitsTransfer -Source https://aka.ms/WACDownload -Destination "$env:USERPROFILE\Downloads\WindowsAdminCenter.msi"
   ```
1. 다음 명령을 입력한 다음 Enter 키를 눌러 Windows Admin Center를 설치합니다.
    
   ```powershell
   Start-Process msiexec.exe -Wait -ArgumentList "/i $env:USERPROFILE\Downloads\WindowsAdminCenter.msi /qn /L*v log.txt REGISTRY_REDIRECT_PORT_80=1 SME_PORT=443 SSL_CERTIFICATE_OPTION=generate"
   ```

   > **참고**: 설치가 완료될 때까지 기다리세요. 이 작업은 2분 정도 걸립니다.

1. **SEA-ADM1** 에서 Microsoft Edge를 시작한 다음 `https://SEA-ADM1.contoso.com`으로 이동합니다. 
1. 메시지가 표시되면 **Windows 보안** 대화 상자에 다음 자격 증명을 입력한 다음 **확인** 을 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. **모든 연결** 창에서 **+ 추가** 를 선택합니다.
1. **리소스 추가 또는 만들기** 창의 **서버** 타일에서 **추가** 를 선택합니다.
1. **서버 이름** 텍스트 상자에 **sea-svr1.contoso.com** 을 입력합니다.
1. **이 연결에 다른 계정 사용** 옵션이 선택되어 있는지 확인하고 다음 자격 증명을 입력한 다음 **자격 증명을 사용하여 추가** 를 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

   > **참고**: 8단계를 수행한 후 **연결 목록에 이 서버를 추가할 수 있지만 사용할 수 있는지 확인할 수 없다는** 오류 메시지가 나타나면 **추가** 를 선택합니다. 모든 연결 창에서 **sea-svr1.contoso.com** 을 선택하고 **다음으로 관리** 를 선택합니다. **자격 증명 지정** 대화 상자에서 **이 연결 옵션에 다른 계정 사용** 옵션이 선택되어 있는지 확인하고 관리자 자격 증명을 입력한 다음 **계속** 을 선택합니다.

   > **참고**: Single Sign-On을 수행하려면 Kerberos 제한 위임을 설정해야 합니다.

1. **sea-svr1.contoso.com** 페이지의 **도구** 목록에서 **가상 머신** 을 선택하고 **요약** 탭을 선택한 다음, 해당 콘텐츠를 검토합니다.
1. **인벤토리** 탭을 선택하고 두 가상 머신 목록이 포함되어 있는지 확인합니다.
1. **SEA-VM1** 을 선택하고 해당 속성 창을 검토합니다.
1. **설정** 을 선택한 다음 **디스크** 를 선택합니다.
1. 창 맨 아래로 스크롤하여 **+ 디스크 추가** 를 선택합니다.
1. **새 가상 하드 디스크** 를 선택합니다.
1. 새 가상 하드 디스크 창의 **크기(GB)** 텍스트 상자에 **5** 를 입력하고 다른 설정은 기본값으로 그대로 두고 **만들기** 를 선택합니다.
1. **디스크 설정 저장** 을 선택한 다음 **닫기** 를 선택합니다.
1. **SEA-VM1** 의 **속성** 창으로 돌아가서 **전원** 을 선택한 다음 **시작** 을 선택하여 **SEA-VM1** 을 시작합니다.
1. 아래로 스크롤하여 실행 중인 VM에 대한 통계를 표시합니다.
1. 페이지를 새로 고치고, **전원** 을 선택하고, **종료** 를 선택한 다음 **예** 를 선택하여 확인합니다.
1. **도구** 목록에서 **가상 스위치** 를 선택하고 창에 두 개의 스위치가 표시되는지 확인합니다.

### <a name="exercise-1-results"></a>연습 1 결과

이 연습이 끝나면 Hyper-V 관리자 및 Windows Admin Center를 사용하여 가상 스위치, 가상 하드 디스크, 가상 머신을 만든 다음 가상 머신을 관리하게 됩니다.

### <a name="exercise-2-installing-and-configuring-containers"></a>연습 2: 컨테이너 설치 및 구성

#### <a name="task-1-install-docker-on-windows-server"></a>작업 1: Windows Server에 Docker 설치

1. **SEA-ADM1** 에서 **SEA-SVR1** 의 **도구** 목록에서 **PowerShell** 을 선택합니다. 메시지가 표시되면 **Pa55w.rd** 를 입력하고 **CONTOSO\\Administrator** 사용자 계정을 사용하여 인증한 다음 Enter 키를 누릅니다. 

   > **참고**: 이렇게 하면 SEA-SVR1에 대한 PowerShell 원격 연결이 설정됩니다.

   > **참고**: Windows Admin Center에서의 Powershell 연결은 랩에서 사용하는 중첩된 가상화 때문에 상대적으로 느릴 수 있습니다. 이 경우 **SEA-ADM1** 의 Windows Powershell 콘솔에서 `Enter-PSSession -ComputerName SEA-SVR1`을 실행하세요.

1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter 키를 눌러 TLS 1.2를 강제로 사용하고 PowerShellGet 모듈을 설치합니다.

   ```powershell
   [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12
   Install-PackageProvider -Name NuGet -Force
   Install-Module PowerShellGet -RequiredVersion 2.2.4 -SkipPublisherCheck
   ```
1. 신뢰할 수 없는 리포지토리에서 모듈 설치를 확인하라는 메시지가 표시되면 **A** 키를 누른 다음 Enter 키를 누릅니다.
1. 설치가 완료되면 다음 명령을 입력한 다음 Enter 키를 눌러 **SEA-SVR1** 을 다시 시작합니다.

   ```powershell
   Restart-Computer -Force
   ```
1. **SEA-SVR1** 이 다시 시작되면 **PowerShell** 도구를 다시 사용하여 **SEA-SVR1** 에 대한 새 PowerShell 원격 세션을 설정합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 실행한 다음 **SEA-SVR1** 에 Docker Microsoft PackageManagement 공급자를 설치합니다.

   ```powershell
   Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
   ```
1. NuGet 공급자 프롬프트에서 **Y** 키를 누른 다음 Enter 키를 누릅니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 실행한 다음 Enter 키를 눌러 **SEA-SVR1** 에 Docker 런타임을 설치합니다.

   ```powershell
   Install-Package -Name docker -ProviderName DockerMsftProvider
   ```
1. 확인하라는 메시지가 표시되면 **A** 키를 누른 다음 Enter 키를 누릅니다.
1. 설치가 완료되면 다음 명령을 입력한 다음 Enter 키를 눌러 **SEA-SVR1** 을 다시 시작합니다.

   ```powershell
   Restart-Computer -Force
   ```

#### <a name="task-2-install-and-run-a-windows-container"></a>작업 2: Windows 컨테이너 설치 및 실행

1. **SEA-SVR1** 이 다시 시작되면 PowerShell 도구를 다시 사용하여 **SEA-SVR1** 에 대한 새 PowerShell 원격 세션을 설정합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 실행한 다음 Enter 키를 눌러 설치된 Docker 버전을 확인합니다.

   ```powershell
   Get-Package -Name Docker -ProviderName DockerMsftProvider
   ```
1. 다음 명령을 입력한 다음 Enter 키를 눌러 현재 **SEA-SVR1** 에 있는 Docker 이미지를 식별합니다. 

   ```powershell
   docker images
   ```

   > **참고**: 로컬 리포지토리 저장소에 이미지가 없는지 확인합니다.

1. 다음 명령을 입력한 다음 Enter 키를 눌러 온라인 Microsoft 리포지토리의 Docker 기본 이미지를 나열합니다.

   ```powershell
   docker search Microsoft
   ```
1. 다음 명령을 입력한 다음 Enter 키를 눌러 IIS(Internet Information Services) 설치가 포함된 Nano 서버 이미지를 다운로드합니다.

   ```powershell
   docker pull nanoserver/iis
   ```

   > **참고**: 다운로드를 완료하는 데 걸리는 시간은 랩 VM에서 Microsoft 컨테이너 레지스트리로 이어지는 네트워크 연결의 가용 대역폭에 따라 달라집니다.

1. 다음 명령을 입력한 다음 Enter 키를 눌러 Docker 이미지가 성공적으로 다운로드되었는지 확인합니다.

   ```powershell
   docker images
   ```
1. 다음 명령을 입력한 다음 Enter 키를 눌러 다운로드한 이미지에 따라 컨테이너를 시작합니다.

   ```powershell
   docker run --isolation=hyperv -d -t --name nano -p 80:80 nanoserver/iis 
   ```

   > **참고**: Docker 명령은 컨테이너를 모든 호스트 운영 체제 비호환성 문제를 해결하는 Hyper-V 격리 모드에서 백그라운드 서비스(`-d`)로 시작하고, 컨테이너 호스트의 포트 80이 컨테이너의 포트 80에 매핑되도록 네트워킹을 구성합니다. 

1. 다음 명령을 입력한 다음 Enter 키를 눌러 컨테이너 호스트의 IP 주소 정보를 검색합니다.

   ```powershell
   ipconfig
   ```

   > **참고**: vEthernet(nat)이라는 이더넷 어댑터의 IPv4 주소를 식별합니다. 이것은 새 컨테이너의 주소입니다. 그런 다음 **Ethernet** 이라는 이더넷 어댑터의 IPv4 주소를 식별합니다. 이것은 호스트(**SEA-SVR1**)의 IP 주소이며 **172.16.10.12** 로 설정됩니다.

1. **SEA-ADM1** 에서 Microsoft Edge 창으로 전환하고 다른 탭을 연 다음 **http://172.16.10.12** 로 이동합니다. 브라우저에서 기본 IIS 페이지가 표시되는지 확인합니다.
1. **SEA-ADM1** 에서 PowerShell 원격 세션을 다시 **SEA-SVR1** 로 전환한 다음 **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter 키를 눌러 실행 중인 컨테이너를 나열합니다.

   ```powershell
   docker ps
   ```
   > **참고**: 이 명령은 현재 **SEA-SVR1** 에서 실행 중인 컨테이너에 관한 정보를 제공합니다. 컨테이너 ID를 기록해 두세요. 컨테이너를 중지하는 용도로 사용해야 합니다. 

1. 다음 명령을 입력한 다음 Enter 키를 눌러 실행 중인 컨테이너를 중지합니다. `<ContainerID>` 자리 표시자를 이전 단계에서 식별한 컨테이너 ID로 대체합니다. 

   ```powershell
   docker stop <ContainerID>
   ```
1. 다음 명령을 입력한 다음 Enter 키를 눌러 컨테이너가 중지되었는지 확인합니다.

   ```powershell
   docker ps
   ```

#### <a name="task-3-use-windows-admin-center-to-manage-containers"></a>작업 3: Windows Admin Center를 사용하여 컨테이너 관리

1. **SEA-ADM1** 의 Windows Admin Center에 있는 **sea-svr1.contoso.com** 의 도구 메뉴에서 **컨테이너** 를 선택합니다. **PowerShell** 세션을 닫기 여부를 묻는 메시지가 표시되면 **계속** 을 선택합니다.
1. 컨테이너 창에서 **요약**, **컨테이너**, **이미지**, **네트워크** 및 **볼륨** 탭을 탐색합니다.

### <a name="exercise-2-results"></a>연습 2 결과

이 연습이 끝나면 Windows Server에 Docker를 설치하고, 웹 서비스가 포함된 Windows 컨테이너 이미지를 다운로드하고, 관련 기능을 확인하게 됩니다.