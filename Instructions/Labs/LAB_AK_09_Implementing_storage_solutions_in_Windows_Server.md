---
lab:
  title: '랩: Windows Server에서 스토리지 솔루션 구현'
  type: Answer Key
  module: 'Module 9: File servers and storage management in Windows Server'
ms.openlocfilehash: 6019d7dac7105bf65a5720b292ef5afc46f4faac
ms.sourcegitcommit: d34dce53481b0263d0ff82913b3f49cb173d5c06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2022
ms.locfileid: "147039379"
---
# <a name="lab-answer-key-implementing-storage-solutions-in-windows-server"></a>랩 응답 키: Windows Server에서 스토리지 솔루션 구현

> **참고**: 각 연습 간에 VM(가상 머신)을 되돌려야 합니다. 대부분의 VM이 Windows Server 2019 Server Core이기 때문에, 되돌리고 다시 시작하는 데 걸리는 시간이 연습에서 스토리지 환경의 변경 사항을 실행 취소하는 것보다 더 빠릅니다.

## <a name="exercise-1-implementing-data-deduplication"></a>연습 1: 데이터 중복 제거 구현

#### <a name="task-1-install-the-data-deduplication-role-service"></a>작업 1: 데이터 중복 제거 역할 서비스 설치

1. **SEA-ADM1** 에 연결한 다음, 필요하다면 **Pa55w.rd** 암호를 이용해 **CONTOSO\\Administrator** 로 로그인합니다.
1. **SEA-ADM1** 에서 **시작을** 선택한 다음 **서버 관리자** 를 선택합니다.
1. 서버 관리자에서 **관리** 를 선택한 다음, **역할 및 기능 추가** 를 선택합니다.
1. **역할 및 기능 추가 마법사** 에서 **다음** 을 두 번 선택합니다.
1. **대상 서버 선택** 페이지의 서버 풀 창에서 **SEA-SVR3.Contoso.com** 을 선택한 후 **다음** 을 선택합니다.
1. **서버 역할 선택** 페이지의 역할 창에서 **파일 및 스토리지 서비스** 항목을 확장한 다음 **파일 및 iSCSI 서비스** 항목을 확장하고 **데이터 중복 제거** 항목을 선택한 후 **다음** 을 선택합니다.
1. **기능 선택** 페이지에서 **다음** 을 선택한 후 **설치 선택 확인** 페이지에서 **설치** 를 선택합니다.
1. 역할 서비스가 설치되는 동안 작업 표시줄에서 **파일 탐색기** 아이콘을 선택합니다.
1. **파일 탐색기** 에서 **C** 드라이브로 이동합니다.
1. **Labfiles** 디렉터리를 선택한 다음 상황에 맞는 메뉴를 표시합니다. 메뉴에서 **액세스 권한 부여** 를 선택한 다음, 계단식 메뉴에서 **특정 사용자** 를 선택합니다.
1. **네트워크 액세스** 창의 **이름 입력에서 추가를 클릭하거나 화살표를 클릭하여 다른 사용자** 텍스트 상자를 찾은 다음 **사용자** 를 입력하고 **추가** 를 클릭합니다.
1. **네트워크 액세스** 창에서 **공유** 를 선택하고 폴더가 **공유된** 창으로 표시되면 **완료** 를 선택합니다.
1. **서버 관리자** 창으로 다시 전환한 다음 **역할 및 기능 추가 마법사 설치 성공** 페이지에서 **닫기** 를 선택합니다.
1. **SEA-SVR3** 콘솔 세션으로 전환한 다음 필요한 경우 **Pa55w.rd** 암호로 **CONTOSO\\Administrator** 로 로그인합니다.
1. **SConfig** 메뉴가 표시되는 경우, **옵션을 선택할 숫자 입력에서** **15** 를 입력하고 Enter 키를 눌러 **PowerShell** 콘솔 세션으로 종료합니다.
1. **Windows PowerShell** 프롬프트에서 다음 명령을 입력하고 각 명령 뒤에 Enter 키를 눌러 ReFS로 포맷된 새 드라이브를 만듭니다.

   ```powershell
   Get-Disk
   Initialize-Disk -Number 1
   New-Partition -DiskNumber 1 -UseMaximumSize -DriveLetter M
   Format-Volume -DriveLetter M -FileSystem ReFS
   ```
1. **Windows PowerShell** 프롬프트에서 다음 명령을 입력하고 각 명령 뒤에 Enter 키를 눌러 **SEA-ADM1** 에서 중복 제거할 샘플 파일을 만들고, 실행하고, 결과를 식별하는 스크립트를 복사합니다.

   ```powershell
   New-PSDrive –Name 'X' –PSProvider FileSystem –Root '\\SEA-ADM1\Labfiles'
   New-Item -Type Directory -Path 'M:\Data' -Force
   Copy-Item -Path X:\Lab09\CreateLabFiles.cmd -Destination M:\Data\ -PassThru
   Start-Process -FilePath M:\Data\CreateLabFiles.cmd -PassThru
   Set-Location -Path M:\Data
   Get-ChildItem -Path .
   Get-PSDrive -Name M
   ```

   > **참고**: **M** 드라이브에서 사용 가능한 공간을 기록해 두세요. 

#### <a name="task-2-enable-and-configure-data-deduplication"></a>작업 2: 데이터 중복 제거 사용 및 구성

1. **SEA-ADM1** 에 대한 콘솔 세션으로 다시 전환한 다음 콘솔 세션 내에서 **서버 관리자** 로 전환합니다.
1. **서버 관리자** 트리 창에서 **파일 및 스토리지 서비스** 를 선택한 다음 **디스크** 를 선택합니다.
1. 디스크 창에서 **SEA-SVR3** 의 디스크 목록을 찾아 이전 작업에서 구성한 디스크 번호 **1** 을 나타내는 항목을 선택합니다.
1. 볼륨 창에서 **M:** 볼륨의 상황에 맞는 메뉴를 표시한 다음, 메뉴에서 **데이터 중복 제거 구성** 을 선택합니다.
1. **볼륨(M:\\) 중복 제거 설정** 창의 **데이터 중복 제거** 드롭다운 목록에서 **범용 파일 서버** 설정을 선택합니다.
1. **이전 파일 중복 제거(일 수):** 텍스트 상자에서 기본값 **3** 을 **0** 으로 바꿉니다.
1. **중복 제거 일정 설정** 단추를 선택합니다.
1. **SEA-SVR3 중복 제거 일정** 창에서 **처리량 최적화 사용** 을 선택한 다음 **확인** 을 선택합니다.
1. 다시 **볼륨(M:\\) 중복 제거 설정** 창에서 **확인** 을 선택합니다.

#### <a name="task-3-test-data-deduplication"></a>작업 3: 데이터 중복 제거 테스트

1. **SEA-ADM1** 에서 **시작** 을 선택한 다음 **Windows PowerShell(관리자)** 을 선택합니다.

   >**참고**: **SEA-ADM1** 에 Windows Admin Center를 아직 설치하지 않았다면 다음 두 단계를 수행합니다.

1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter 키를 눌러 Windows Admin Center의 최신 버전을 다운로드합니다.
    
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

1. 모든 연결 창에서 **+ 추가** 를 선택합니다.
1. 리소스 추가 또는 만들기 창의 **서버** 타일에서 **추가** 를 선택합니다.
1. **서버 이름** 텍스트 상자에 **sea-svr3.contoso.com** 을 입력합니다. 
1. 필요한 경우 **이 연결에 다른 계정 사용** 옵션이 선택되어 있는지 확인하고 다음 자격 증명을 입력한 다음 **자격 증명을 사용하여 추가** 를 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. **sea-svr3.contoso.com** 페이지의 **도구** 메뉴에서 **PowerShell** 을 선택한 다음 메시지가 표시되면 암호로 **Pa55w.rd** 를 사용하여 **CONTOSO\\Administrator** 사용자로 로그인합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter 키를 눌러 중복 제거를 트리거합니다.

   ```powershell
   Start-DedupJob -Volume M: -Type Optimization –Memory 50
   ```
1. **SEA-SVR3** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **SEA-SVR3** 의 **Windows PowerShell** 프롬프트에서 다음 명령을 입력하고 Enter 키를 눌러 중복 제거되는 볼륨에서 사용 가능한 공간을 식별합니다.

   ```powershell
   Get-PSDrive -Name M
   ```

   > **참고**: 이전에 표시된 값을 현재 값과 비교합니다. 

1. 중복 제거 작업이 완료되고 이전 단계를 반복할 수 있도록 5~10분 정도 기다립니다.
1. **SEA-ADM1** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **SEA-ADM1** 의 **Windows PowerShell** 콘솔에서 **sea-svr3.contoso.com** 에 대한 Windows Admin Center 연결을 표시하는 **Microsoft Edge** 창 내에 다음 명령을 입력하고 각 명령 뒤에 Enter 키를 눌러 중복 제거 작업의 상태를 확인합니다.

   ```powershell
   Get-DedupStatus –Volume M: | fl
   Get-DedupVolume –Volume M: |fl
   Get-DedupMetadata –Volume M: |fl
   ```
1. **SEA-ADM1** 에서 **서버 관리자** 의 디스크 창으로 전환한 다음 오른쪽 위 모서리에 있는 **작업** 메뉴에서 **새로 고침** 을 선택합니다.
1. **볼륨** 섹션에서 **M:** 볼륨을 선택하고 상황에 맞는 메뉴를 표시하고 메뉴에서 **속성** 을 선택합니다. 
1. **볼륨(M:\\) 속성** 창에서 **중복 제거 비율** 및 **중복 제거 절감** 에 대한 값을 검토합니다.

## <a name="exercise-2-configuring-iscsi-storage"></a>연습 2: iSCSI 스토리지 구성

#### <a name="task-1-install-iscsi-and-configure-targets"></a>작업 1: iSCSI 설치 및 대상 구성

1. **SEA-ADM1** 에서 **Windows PowerShell** 창으로 전환합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력하고 Enter 키를 눌러 **SEA-SVR3** 에 대한 PowerShell 원격 세션을 설정합니다.

   ```powershell
   Enter-PSSession -ComputerName SEA-SVR3
   ```
1. 다음 명령을 입력하고 Enter 키를 눌러 **SEA-SVR3** 에 iSCSI 대상을 설치합니다.

   ```powershell
   Install-WindowsFeature –Name FS-iSCSITarget-Server –IncludeManagementTools
   ```
1. 다음 명령을 입력하고 각 명령 뒤에 Enter 키를 눌러 디스크 2에 ReFS로 포맷된 새 볼륨을 만듭니다.

   ```powershell
   Initialize-Disk -Number 2
   $partition2 = New-Partition -DiskNumber 2 -UseMaximumSize -AssignDriveLetter
   Format-Volume -DriveLetter $partition2.DriveLetter -FileSystem ReFS
   ```
1. 다음 명령을 입력하고 각 명령 뒤에 Enter 키를 눌러 디스크 3에 ReFS로 포맷된 새 볼륨을 만듭니다.

   ```powershell
   Initialize-Disk -Number 3
   $partition3 = New-Partition -DiskNumber 3 -UseMaximumSize -AssignDriveLetter
   Format-Volume -DriveLetter $partition3.DriveLetter -FileSystem ReFS
   ```
1. 다음 명령을 입력하고 각 명령 뒤에 Enter 키를 눌러 iSCSI 트래픽을 허용하는 고급 보안 규칙을 사용하여 Windows Defender 방화벽을 구성합니다.

   ```powershell
   New-NetFirewallRule -DisplayName "iSCSITargetIn" -Profile "Any" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 3260
   New-NetFirewallRule -DisplayName "iSCSITargetOut" -Profile "Any" -Direction Outbound -Action Allow -Protocol TCP -LocalPort 3260
   ```
1. 다음 명령을 입력하고 Enter 키를 눌러 새로 만든 볼륨에 할당된 드라이브 문자를 표시합니다.

   ```powershell
   $partition2.DriveLetter
   $partition3.DriveLetter
   ```

   > **참고**: 지침에서는 드라이브 문자가 각각 **E** 및 **F** 라고 가정합니다. 드라이브 문자 할당이 다른 경우, 이 연습의 지침에 따르는 동안 이 점을 고려합니다.

#### <a name="task-2-connect-to-and-configure-iscsi-targets"></a>작업 2: iSCSI 대상 연결 및 구성

1. **SEA-ADM1** 에서 **서버 관리자** 로 전환합니다.
1. **SEA-ADM1** 에서 **서버 관리자** 의 디스크 창으로 전환한 다음 오른쪽 위 모서리에 있는 **작업** 메뉴에서 **새로 고침** 을 선택합니다.
1. **서버 관리자** 에서, 트리 창에서 선택한 **파일 및 스토리지 서비스** 의 **디스크** 를 사용하여 오른쪽 위 모서리에 있는 **작업** 메뉴에서 **새로 고침** 을 선택합니다.
1. 온라인 상태에서 디스크 2 및 3을 사용하여 업데이트된 **SEA-SVR3** 디스크 구성을 검토합니다.
1. **SEA-DC1** 항목으로 이동하고 디스크 구성을 검토합니다.

   > **참고**: **SEA-DC1** 에는 부팅 및 시스템 볼륨을 호스팅하는 디스크가 하나만 있습니다.

1. **서버 관리자** 의 **파일 및 스토리지 서비스** 에서 **iSCSI** 를 선택하고 **작업** 을 선택한 다음 드롭다운 메뉴에서 **새 iSCSI 가상 디스크** 를 선택합니다.
1. **새 iSCSI 가상 디스크 마법사** 에서 **iSCSI 가상 디스크 위치 선택** 페이지의 **SEA-SVR3** 서버 아래에서 **E:** 볼륨을 선택한 후 **다음** 을 선택합니다.
1. **iSCSI 가상 디스크 이름 지정** 페이지의 **이름** 텍스트 상자에 **iSCSIDisk1** 을 입력한 후 **다음** 을 선택합니다.
1. **iSCSI 가상 디스크 크기 지정** 페이지의 **크기** 텍스트 상자에 **5** 를 입력합니다. 다른 모든 설정은 기본값으로 두고 **다음** 을 선택합니다.
1. **iSCSI 대상 할당** 페이지에서 **새 iSCSI 대상** 라디오 단추가 선택되어 있는지 확인한 후 **다음** 을 선택합니다.
1. **대상 이름 지정** 페이지의 **이름** 필드에 **iSCSIFarm** 을 입력한 후 **다음** 을 선택합니다.
1. **액세스 서버 지정** 페이지에서 **추가** 단추를 선택합니다.
1. **초기자를 식별하는 메서드 선택** 창에서 **찾아보기** 단추를 선택합니다.
1. **컴퓨터 선택** 창의 **선택할 개체 이름 입력** 텍스트 상자에 **SEA-DC1** 을 입력하고 **이름 확인** 을 선택한 다음 **확인** 을 선택합니다.
1. **초기자를 식별하는 메서드 선택** 창에서 **확인** 단추를 선택합니다.
1. **액세스 서버 지정** 페이지에서 **다음** 을 선택합니다.
1. **인증 사용** 페이지에서 **다음** 을 선택합니다.
1. **선택 확인** 페이지에서 **만들기** 를 선택합니다.
1. **결과 보기** 페이지에서 **닫기** 를 선택합니다.
1. 다음 설정을 사용하여 6-9단계를 반복하고, 기존 iSCSI 대상을 선택하고, 18-19단계를 사용하여 마법사를 완료하여 두 번째 iSCSI 가상 디스크(F:)를 만듭니다.

   - 스토리지 위치: **F:**
   - 이름: **iSCSIDisk2**
   - 디스크 크기: **5GB**, **동적 확장**
   - iSCSI 대상: **iSCSIFarm**

1. **SEA-DC1** 콘솔 세션으로 전환한 다음 필요한 경우 **Pa55w.rd** 암호를 사용하여 **CONTOSO\\Administrator** 로 로그인합니다.
1. **SConfig** 메뉴가 표시되는 경우, **옵션을 선택할 숫자 입력에서** **15** 를 입력하고 Enter 키를 눌러 **PowerShell** 콘솔 세션으로 종료합니다.
1. **Windows PowerShell** 프롬프트에서 다음 명령을 입력하고 각 명령 뒤에 Enter 키를 눌러 iSCSI 초기자 서비스를 시작하고 iSCSI 초기자 구성을 표시합니다.

   ```powershell
   Start-Service -Name MSiSCSI
   iscsicpl
   ```

   > **참고**: **iscsicpl** 명령은 **iSCSI 초기자 속성** 창을 엽니다.

1. **SEA-DC1** 에서 **iSCSI 초기자 속성** 대화 상자의 **대상** 탭에 있는 **대상** 텍스트 상자에 **SEA-SVR3.contoso.com** 을 입력한 다음 **빠른 연결** 을 선택합니다.
1. **빠른 연결** 대화 상자에서 **검색된 대상 이름** 이 **iqn.1991-05.com.microsoft:sea-svr3-iscscifarm-target** 인지 살펴본 다음 **완료** 를 선택합니다.
1. **iSCSI 초기자 속성** 대화 상자에서 **확인** 을 선택합니다.

#### <a name="task-3-verify-iscsi-disk-configuration"></a>작업 3: iSCSI 디스크 구성 확인

1. **서버 관리자** 창이 활성화된 상태에서 **SEA-ADM1** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **서버 관리자** 에서, **파일 및 스토리지 서비스의** iSCSI 창에서 디스크 창으로 전환한 다음 오른쪽 위 모서리에 있는 **작업** 메뉴에서 **새로 고침** 을 선택합니다. 
1. **SEA-DC1** 디스크 구성을 검토하고 **오프라인** 상태에서 **iSCSI** 버스 유형인 **5 GB** 디스크를 두 개 포함하고 있는지 확인합니다.
1. **SEA-DC1** 콘솔 세션으로 전환합니다. 
1. **Windows PowerShell** 프롬프트에서 다음 명령을 입력하고 Enter 키를 눌러 디스크 구성을 나열합니다.

   ```powershell
   Get-Disk
   ```

   > **참고**: 두 디스크가 모두 존재하고 정상적이지만 오프라인 상태입니다. 이 두 디스크를 사용하려면 초기화하고 포맷해야 합니다.

1. 다음 명령을 입력하고 각 명령 뒤에 Enter 키를 눌러 드라이브 문자 **E** 를 사용하여 ReFS로 포맷된 볼륨을 만듭니다.

   ```powershell
   Initialize-Disk -Number 1
   New-Partition -DiskNumber 1 -UseMaximumSize -DriveLetter E
   Format-Volume -DriveLetter E -FileSystem ReFS
   ```
1. 이전 단계를 반복하여 ReFS로 포맷된 새 드라이브를 만들되 이번에는 디스크 번호 **2** 와 드라이브 문자 **F** 를 사용합니다.
1. **서버 관리자** 창이 활성화된 상태에서 **SEA-ADM1** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **서버 관리자** 에서 창의 오른쪽 위 모서리에 있는 **작업** 메뉴에서 **새로 고침** 을 선택하여 **파일 및 스토리지 서비스** 의 디스크 창을 새로 고침합니다. 
1. **SEA-DC1** 디스크 구성을 검토하고 두 드라이브 모두 이제 **온라인** 상태인지 확인합니다.

#### <a name="task-4-revert-disk-configuration"></a>작업 4: 디스크 구성 되돌리기 

1. **SEA-SVR3** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **Windows PowerShell** 프롬프트에서 다음 명령을 입력하고 각 명령 뒤에 Enter 키를 눌러 **SEA-SVR3** 의 디스크를 원래 상태로 재설정합니다(**Clear-Disk** cmdlet의 실행을 확인하라는 메시지가 표시되면 **Y** 키를 누른 다음 각 루프 반복 전에 Enter 키를 누릅니다.)

   ```powershell
   for ($num = 1;$num -le 4; $num++) {Clear-Disk -Number $num -RemoveData -RemoveOEM -ErrorAction SilentlyContinue}
   for ($num = 1;$num -le 4; $num++) {Set-Disk -Number $num -IsOffline $true}
   ```

   > **참고**: 다음 연습을 준비하려면 이 작업이 필요합니다.

## <a name="exercise-3-configuring-redundant-storage-spaces"></a>연습 3: 중복 스토리지 공간 구성

#### <a name="task-1-create-a-storage-pool"></a>작업 1: 스토리지 풀 만들기

1. **서버 관리자** 창이 활성화된 상태에서 **SEA-ADM1** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **서버 관리자** 에서 창의 오른쪽 위 모서리에 있는 **작업** 메뉴에서 **새로 고침** 을 선택하여 **파일 및 스토리지 서비스** 의 디스크 창을 새로 고침합니다. 
1. 디스크 창에서 아래로 스크롤하여 **SEA-SVR3** 디스크 1~4가 **알 수 없는** 파티션 및 **오프라인** 상태로 나열되어 있음에 주목합니다. 
1. 4개의 디스크를 차례로 선택하고 상황에 맞는 메뉴를 표시하고 메뉴에서 **온라인 상태로 전환** 옵션을 선택한 다음 **디스크를 온라인 상태로 전환** 창에서 **예** 를 선택합니다.
1. 모든 디스크가 **온라인** 상태로 나열되는지 확인합니다. **서버 관리자** 의 탐색 창에서 **스토리지 풀을** 선택합니다.
1. **서버 관리자** 에서 **스토리지 풀** 영역의 **작업** 목록에서 **새 스토리지 풀** 을 선택합니다. 
1. **새 스토리지 풀 마법사** 의 **시작하기 전에** 페이지에서 **다음** 을 선택합니다.
1. **스토리지 풀 이름 및 하위 시스템 지정** 페이지의 **이름** 텍스트 상자에 **SP1** 을 입력합니다. **설명** 텍스트 상자에 **스토리지 풀 1** 을 입력합니다. **사용하려는 사용 가능한 디스크 그룹(원시 풀이라고도 함) 선택** 목록에서 **SEA-SVR3** 항목을 선택한 후 **다음** 을 선택합니다.
1. **스토리지 풀용 실제 디스크 선택** 페이지에서 **127GB** 크기의 디스크 3개 옆에 있는 확인란을 선택한 후 **다음** 을 선택합니다.
1. **선택 확인** 페이지에서 설정을 검토한 다음 **만들기** 를 선택합니다.
1. **닫기** 를 선택합니다.

#### <a name="task-2-create-a-volume-based-on-a-three-way-mirrored-disk"></a>작업 2: 3방향 미러 디스크를 기반으로 볼륨 만들기 

1. **SEA-ADM1** 의 **서버 관리자** 에 있는 스토리지 풀 창에서 **SP1** 을 선택합니다.
1. **가상 디스크** 영역에서 **작업** 을 선택한 다음 **새 가상 디스크** 를 선택합니다.
1. **스토리지 풀 선택** 대화 상자에서 **SP1** 을 선택한 다음 **확인** 을 선택합니다.
1. **새 가상 디스크 마법사** 의 **시작하기 전에** 페이지에서 **다음** 을 선택합니다.
1. **가상 디스크 이름 지정** 페이지의 **이름** 텍스트 상자에 **Three-Mirror** 를 입력한 후 **다음** 을 선택합니다.
1. **엔클로저 복원력 지정** 페이지에서 **다음** 을 선택합니다.
1. **스토리지 레이아웃 선택** 페이지에서 **미러** 를 선택한 후 **다음** 을 선택합니다.
1. **프로비저닝 유형 지정** 페이지에서 **씬** 을 선택한 후 **다음** 을 선택합니다.
1. **가상 디스크의 크기 지정** 페이지의 **크기 지정** 텍스트 상자에 **25** 를 입력한 후 **다음** 을 선택합니다.
1. **선택 확인** 페이지에서 설정을 검토한 다음 **만들기** 를 선택합니다.
1. **결과 보기** 페이지에서 **이 마법사가 닫히면 볼륨 만들기** 확인란의 선택을 취소하고 **닫기** 를 선택합니다.
1. **서버 관리자** 의 탐색 창에서 **볼륨** 항목이 선택되어 있는지 확인합니다.
1. **볼륨** 영역에서 **작업** 을 선택한 다음 **새 볼륨** 을 선택합니다.
1. **새 볼륨 마법사** 의 **시작하기 전에** 페이지에서 **다음** 을 선택합니다.
1. **서버 및 디스크 선택** 페이지에서 **SEA-SVR3** 을 선택하고 **Three-Mirror** 를 선택한 후 **다음** 을 선택합니다.
1. **볼륨 크기 지정** 페이지에서 **다음** 을 선택합니다.
1. **드라이브 문자 또는 폴더에 할당** 페이지에서 **드라이브 문자** 를 선택하고 **T** 를 선택한 후 **다음** 을 선택합니다.
1. **파일 시스템 설정 선택** 페이지의 **파일 시스템** 드롭다운 목록에서 **ReFS** 를 선택합니다. **볼륨 레이블** 텍스트 상자에 **TestData** 를 입력한 후 **다음** 을 선택합니다.
1. **데이터 중복 제거 사용** 페이지에서 **다음** 을 선택합니다.
1. **선택 확인** 페이지에서 **만들기** 를 선택합니다.
1. **완료** 페이지에서 **닫기** 를 선택합니다.

#### <a name="task-3-manage-a-volume-in-file-explorer"></a>작업 3: 파일 탐색기에서 볼륨 관리

1. **SEA-ADM1** 에서, **SEA-SVR3** 에 PowerShell 원격 세션을 호스팅하는 **Windows PowerShell** 로 전환합니다.
1. **Windows PowerShell** 콘솔의 **[SEA-SVR3]** 프롬프트에서 다음 명령을 입력하고 Enter 키를 눌러 고급 보안으로 Windows Defender 방화벽의 모든 파일 및 프린터 공유 규칙을 사용하도록 설정합니다.

   ```powershell
   Enable-NetFirewallRule -Group "@FirewallAPI.dll,-28502"
   ```

1. **SEA-ADM1** 의 작업 표시줄에서 **파일 탐색기** 아이콘을 선택합니다.
1. **파일 탐색기** 창의 **주소 표시줄** 에서 **\\\\SEA-SVR3.contoso.com\\t$** 를 입력합니다.
1. 파일 탐색기의 세부 정보 창에서 상황에 맞는 메뉴를 표시한 다음 메뉴에서 **새 폴더** 를 선택합니다. 새 폴더에 할당된 기본 이름을 **TestData** 로 바꾼 다음 Enter 키를 누릅니다.
1. 파일 탐색기에서 새로 만든 **TestData** 폴더를 두 번 클릭합니다.
1. 파일 탐색기의 세부 정보 창에서 상황에 맞는 메뉴를 표시한 다음 메뉴에서 **새로 만들기** 를 선택한 다음 **텍스트 문서** 를 선택합니다. 새 파일에 할당된 기본 이름을 **TestDocument** 로 바꾼 다음 Enter 키를 누릅니다.

#### <a name="task-4-disconnect-a-disk-from-the-storage-pool-and-verify-volume-availability"></a>작업 4: 스토리지 풀에서 디스크 연결 끊기 및 볼륨 가용성 확인 

1. **SEA-ADM1** 에서 **서버 관리자** 로 전환합니다. **파일 및 스토리지 서비스** 트리 창에서 **스토리지 풀** 을 선택한 다음 **SP1** 을 선택합니다.
1. 실제 디스크 창에서 **작업** 드롭다운 목록을 선택한 다음 **실제 디스크 추가** 를 선택합니다.
1. **실제 디스크 추가** 대화 상자에서, 풀에 추가할 네 번째 디스크를 나타내는 행에서 디스크 이름 옆에 있는 확인란을 선택합니다. **할당** 드롭다운 목록에서 **자동** 항목이 선택되어 있는지 확인한 다음 **확인** 을 선택합니다.
1. 실제 디스크 창의 목록에서 위쪽 디스크를 마우스 오른쪽 단추로 클릭한 다음 **디스크 제거** 를 선택합니다.
1. **실제 디스크 제거** 창에서 **예** 를 선택합니다.
1. **실제 디스크 제거** 대화 상자에서 **Windows가 영향을 받는 가상 디스크를 복구하고 있습니다** 라는 메시지를 검토한 다음 **확인** 을 선택합니다.
1. **TestData** 폴더의 내용을 표시하는 **파일 탐색기** 창으로 다시 전환합니다.
1. **TestDocument.txt** 를 열고 해당 콘텐츠를 계속 사용할 수 있는지 확인합니다.

#### <a name="task-5-add-a-disk-to-the-storage-pool-and-verify-volume-availability"></a>작업 5: 스토리지 풀에 디스크 추가 및 볼륨 가용성 확인 

1. **SEA-ADM1** 에서 **서버 관리자** 로 전환합니다. **파일 및 스토리지 서비스** 트리 창에서, **스토리지 풀** 항목이 선택된 상태에서 오른쪽 위 모서리에 있는 **작업** 메뉴에서 **스토리지 다시 검사** 를 선택합니다.
1. 메시지가 표시되면 **스토리지 다시 검사** 대화 상자에서 **예** 를 선택합니다.
1. 실제 디스크 창에서 **작업** 을 선택한 다음, 드롭다운 메뉴에서 **실제 디스크 추가** 를 선택합니다.
1. **실제 디스크 추가** 창에서, 풀에 추가할 네 번째 디스크를 나타내는 행에서 디스크 이름 옆에 있는 확인란을 선택합니다. **할당** 드롭다운 목록에서 **자동** 항목이 선택되어 있는지 확인한 다음 **확인** 을 선택합니다.
1. **SEA-ADM1** 에서 **파일 탐색기** 창으로 전환하고 **TestData** 폴더 및 해당 콘텐츠를 계속 사용할 수 있는지 확인합니다.

#### <a name="task-6-revert-disk-configuration"></a>작업 6: 디스크 구성 되돌리기 

1. **SEA-SVR3** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **Windows PowerShell** 프롬프트에서 다음 명령을 입력하고 각 명령 뒤에 Enter 키를 눌러 **SEA-SVR3** 의 디스크를 원래 상태로 재설정합니다(**Remove-VirtualDisk**, **Remove-StoragePool** 및 **Clear-Disk** cmdlet의 실행을 확인하라는 메시지가 표시되면 **Y** 키를 누른 다음 각 cmdlet 실행 전에 Enter 키를 누릅니다).

   ```powershell
   Get-VirtualDisk -FriendlyName 'Three-Mirror' | Remove-VirtualDisk
   Get-StoragePool -FriendlyName 'SP1' | Remove-StoragePool
   for ($num = 1;$num -le 4; $num++) {Clear-Disk -Number $num -RemoveData -RemoveOEM -ErrorAction SilentlyContinue}
   for ($num = 1;$num -le 4; $num++) {Set-Disk -Number $num -IsOffline $true}
   ```

   > **참고**: 다음 연습을 준비하려면 이 작업이 필요합니다.

## <a name="exercise-4-implementing-storage-spaces-direct"></a>연습 4: 스토리지 공간 다이렉트 구현

#### <a name="task-1-prepare-for-installation-of-storage-spaces-direct"></a>작업 1: 스토리지 공간 다이렉트 설치 준비 

1. **SEA-ADM1** 에 대한 콘솔 세션으로 다시 전환하고 **모든 서버** 를 선택합니다.
1. **SEA-ADM1** 에서, **서버 관리자** 의 콘솔 트리에서 **모든 서버** 를 선택하고, 계속하기 전에 **SEA-SVR1**, **SEA-SVR2** 및 **SEA-SVR3** 의 **관리 효율성** 상태가 **온라인 - 성능 카운터가 시작되지 않음** 인지 확인합니다.
1. **서버 관리자** 의 탐색 창에서 **파일 및 스토리지 서비스** 를 선택한 다음 **디스크** 를 선택합니다.
1. 디스크 창을 선택한 상태에서 오른쪽 위 모서리의 **작업** 메뉴에서 **새로 고침** 을 선택합니다.
1. 디스크 창에서 **SEA-SVR3** 디스크 목록 1~4까지 아래로 스크롤하고 **파티션** 열의 해당 항목이 **알 수 없음** 으로 나열되는지 확인합니다.
1. 4개의 디스크를 각각 차례로 선택한 다음 상황에 맞는 메뉴를 표시합니다. 메뉴에서 **온라인 상태로 전환** 옵션을 선택한 다음 **디스크를 온라인 상태로 전환** 창에서 **예** 를 선택합니다.
1. 동일한 방법을 사용하여 **SEA-SVR1** 및 **SEA-SVR2** 의 모든 디스크를 온라인으로 전환합니다.
1. **SEA-ADM1** 에서 **시작** 을 선택하고 **시작** 메뉴에서 **Windows PowerShell ISE** 를 선택합니다.
1. **Windows PowerShell ISE** 에서 **파일** 메뉴를 선택합니다. **파일** 메뉴에서 **열기** 를 선택한 다음 **열기** 대화 상자에서 **C:\Labfiles\Lab09** 로 이동합니다.
1. **Implement-StorageSpacesDirect.ps1** 를 선택한 다음 **열기** 를 선택합니다.

   > **참고**: 스크립트는 번호가 매겨진 단계로 나뉩니다. 8단계가 있으며 각 단계마다 여러 명령이 있습니다. 개별 줄을 실행하려면 해당 줄 내의 아무 곳에나 커서를 놓고 F8 키를 누르거나 **Windows PowerShell ISE** 창의 도구 모음에서 **선택 항목 실행** 을 선택할 수 있습니다. 여러 줄을 실행하려면 전체 줄을 모두 선택한 다음 F8 또는 **선택 항목 실행** 도구 모음 아이콘을 사용합니다. 단계의 순서는 이 연습의 지침에 설명되어 있습니다. 다음 단계를 시작하기 전에 각 단계가 완료되었는지 확인합니다.

1. 1단계에서 첫 번째 줄을 선택한 다음 F8 키를 눌러 **SEA-SVR1**, **SEA-SVR2**, 및 **SEA-SVR3** 에 **파일 서버 역할 및 장애 조치(Failover) 클러스터링** 기능을 설치합니다.

   > **참고**: 설치가 끝날 때까지 기다리세요. 이 작업은 2분 정도 걸립니다. 각 명령의 출력에서 **Success** 속성이 **True** 로 설정되어 있는지 확인합니다.

1. 1단계에서 두 번째 줄을 선택한 다음 F8 키를 눌러 **SEA-SVR1**, **SEA-SVR2**, 및 **SEA-SVR3** 을 다시 시작합니다.

   > **참고**: 두 번째 명령을 호출하여 서버를 다시 시작하면, 세 번째 명령을 실행하여 다시 시작이 완료되기를 기다리지 않고 장애 조치(failover) 클러스터링 관리 도구를 설치할 수 있습니다.

1. **설치** 부터 시작하여 1단계에서 세 번째 줄을 선택한 다음 F8 키를 눌러 **장애 조치(failover) 클러스터 관리자** 도구를 **SEA-ADM1** 에 설치합니다.

   > **참고**: 서버를 다시 시작하고 **장애 조치(failover) 클러스터 관리자** 도구가 **SEA-ADM1** 에 설치되는 동안 몇 분 정도 기다립니다.

#### <a name="task-2-create-and-validate-a-failover-cluster"></a>작업 2: 장애 조치(failover) 클러스터 만들기 및 유효성 검사 

1. **SEA-ADM1** 에서 **서버 관리자** 창으로 전환합니다.
1. **서버 관리자** 에서 **도구** 를 선택한 다음 **장애 조치(failover) 클러스터 관리자** 를 선택하여 설치가 성공적으로 완료되었는지 확인합니다.
1. **SEA-ADM1** 에서 **관리자: Windows PowerShell ISE** 창으로 전환하고 **Test-Cluster** 로 시작하는 2단계의 줄을 선택한 다음, F8 키를 눌러 클러스터 유효성 검사 테스트를 호출합니다.

   > **참고**: 테스트가 완료될 때까지 기다리세요. 이 작업은 2분 정도 걸립니다. 실패한 테스트가 없는지 확인합니다. 이러한 경고는 예상되기 때문에 무시합니다. 

1. **관리자: Windows PowerShell ISE** 창에서 **New-Cluster** 로 시작하는 3단계의 줄을 선택한 다음 F8 키를 눌러 클러스터를 만듭니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이 작업은 2분 정도 걸립니다. 

1. **SEA-ADM1** 에서, **장애 조치(failover) 클러스터 관리자** 창으로 전환합니다. 작업 창에서 **클러스터에 연결** 을 선택하고 **S2DCluster.Contoso.com** 을 입력한 다음 **확인** 을 선택합니다.

#### <a name="task-3-enable-storage-spaces-direct"></a>작업 3: 스토리지 공간 다이렉트 사용 설정

1. **SEA-ADM1** 에서, **관리자: Windows PowerShell ISE** 창으로 전환하고 **Invoke-Command** 로 시작하는 4단계의 줄을 선택한 다음, F8 키를 눌러 새로 설치된 클러스터에서 스토리지 공간 다이렉트를 사용하도록 설정합니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이 작업은 1분 정도 걸립니다.

1. **관리자: Windows PowerShell ISE** 창에서, **Invoke-Command** 로 시작하는 5단계의 줄을 선택한 다음 F8 키를 눌러 **S2DStoragePool** 을 만듭니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이는 1분 이내에 완료됩니다. 명령의 출력에서 **FriendlyName** 특성의 값이 **S2DStoragePool** 인지 확인합니다.

1. **장애 조치(failover) 클러스터 관리자** 창으로 전환하고, **, S2DCluster.Contoso.com** 을 확장하고, **스토리지** 를 확장한 다음, **풀** 을 선택합니다.
1. **클러스터 풀 1** 이 있는지 확인합니다.
1. **관리자: Windows PowerShell ISE** 창에서, **Invoke-Command** 로 시작하는 6단계의 줄을 선택한 다음 F8 키를 눌러 가상 디스크를 만듭니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이는 1분 이내에 완료됩니다. 

1. **장애 조치(failover) 클러스터 관리자** 창으로 전환한 다음, **스토리지** 노드 내에서 **디스크** 를 선택합니다.
1. **CSV(Cluster Virtual Disk)** 가 있는지 확인합니다.

#### <a name="task-4-create-a-storage-pool-a-virtual-disk-and-a-share"></a>작업 4: 스토리지 풀, 가상 디스크 및 공유 만들기

1. **관리자: Windows PowerShell ISE** 창에서, **Invoke-Command** 로 시작하는 7단계의 줄을 선택한 다음 F8 키를 눌러 파일 서버 클러스터 역할을 만듭니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이는 1분 이내에 완료됩니다. 

1. 명령의 출력에 **S2D-SOFS** 로 설정된 **FriendlyName** 특성과 함께, 역할 정의가 포함되어 있는지 확인합니다. 이를 통해 명령이 성공했는지 여부를 확인합니다.
1. **장애 조치(failover) 클러스터 관리자** 창으로 전환하고 **역할** 을 선택합니다.
1. S2D-SOFS 역할이 있는지 확인합니다. 이 또한 명령이 성공적으로 완료되었는지 여부를 확인합니다.
1. **관리자: Windows PowerShell ISE** 창에서, **Invoke-Command** 로 시작하는 8단계의 세 줄을 선택한 다음 F8 키를 눌러 파일 공유를 만듭니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이는 1분 이내에 완료됩니다. 

1. 명령의 출력에 **C:\\ClusterStorage\\CSV\\VM01** 로 설정된 **경로** 특성과 함께 파일 공유의 정의가 포함되어 있는지 확인합니다. 이를 통해 명령이 성공적으로 완료되었는지 여부를 확인합니다.
1. **장애 조치(failover) 클러스터 관리자** 창의 **역할** 창에서 **이름** 열 아래 **S2D-SOFS** 를 선택한 다음 **공유** 탭을 선택합니다.
1. 이름이 **VM01** 인 공유가 있는지 확인합니다. 이 또한 명령이 성공적으로 완료되었는지 여부를 확인합니다.

#### <a name="task-5-verify-storage-spaces-direct-functionality"></a>작업 5: 스토리지 공간 다이렉트 기능 확인

1. **SEA-ADM1** 의 작업 표시줄에서 **파일 탐색기** 아이콘을 선택합니다.
1. **파일 탐색기** 의 주소 표시줄에 **\\\\S2D-SOFS.contoso.com\\VM01** 을 입력한 다음 Enter 키를 눌러 대상 파일 공유를 엽니다.
1. **파일 탐색기** 의 세부 정보 창에서 상황에 맞는 메뉴를 표시한 다음 메뉴에서 **새 폴더** 를 선택합니다. 새 폴더에 할당된 기본 이름을 **VMFolder** 로 바꾼 다음 Enter 키를 누릅니다.
1. **SEA-ADM1** 에서 **관리자: Windows PowerShell ISE** 창으로 전환합니다.
1. **관리자: Windows PowerShell ISE** 창의 콘솔 창에서 다음 명령을 입력한 다음 Enter 키를 눌러 **SEA-SVR3** 을 종료합니다.

   ```powershell
   Stop-Computer -ComputerName SEA-SVR3 -Force
   ```

1. **SEA-ADM1** 에서 **서버 관리자** 창으로 전환하고 **모든 서버** 를 선택한 다음, 오른쪽 위 모서리에 있는 **작업** 메뉴에서 **새로 고침** 을 선택합니다.
1. **SEA-SVR3** 항목에서 **관리 효율성** 열에 **대상 컴퓨터에 액세스할 수 없음** 항목이 있는지 확인합니다.
1. **파일 탐색기** 창으로 다시 전환하고 **VMFolder** 에 계속 액세스할 수 있는지 확인합니다.
1. **장애 조치(failover) 클러스터 관리자** 로 전환하고 **디스크** 를 선택한 다음 **CSV(Cluster Virtual Disk)** 를 선택합니다.
1. **CSV(Cluster Virtual Disk)** 의 경우 **정상 상태** 가 **경고** 로 설정되고 **작동 상태** 가 **성능 저하됨** 으로 설정됩니다(**작동 상태** 는 **완료되지 않음** 으로 표시될 수도 있음).
1. **SEA-ADM1** 에서 Windows Admin Center가 표시된 **Microsoft Edge** 창으로 전환합니다. 
1. 모든 연결 창으로 이동하고 **+ 추가** 를 선택합니다.
1. **리소스 추가 또는 만들기** 창의 **서버 클러스터** 창에서 **추가** 를 선택합니다.
1. **클러스터 이름** 텍스트 상자에 **S2DCluster.Contoso.com** 을 입력합니다.
1. **이 연결에 다른 계정 사용** 옵션이 선택되어 있는지 확인하고 다음 자격 증명을 입력한 다음 **계정을 사용하여 연결** 을 선택합니다:

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**
   
1. 또한 **클러스터에 서버 추가** 를 선택 취소하고 **추가** 를 선택합니다.
1. **모든 연결** 페이지로 돌아가 **s2dcluster.contoso.com** 을 선택합니다.
1. 페이지가 로드되면 대시보드 창에 **SEA-SVR3** 에 연결할 수 없음을 나타내는 경고가 있는지 확인합니다. 
1. **SEA-SVR3** 에 대한 콘솔 세션으로 전환한 다음 시작합니다. 

   > **참고**: 경고가 자동으로 제거되는 데 몇 분 정도 걸릴 수 있습니다.

1. Windows Admin Center가 표시된 브라우저 페이지를 새로 고치고 모든 서버가 정상인지 확인합니다.
