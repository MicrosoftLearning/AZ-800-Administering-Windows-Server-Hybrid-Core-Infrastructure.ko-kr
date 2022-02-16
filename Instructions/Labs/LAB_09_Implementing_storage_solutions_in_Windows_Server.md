---
lab:
  title: '랩: Windows Server에서 스토리지 솔루션 구현'
  module: 'Module 9: File servers and storage management in Windows Server'
ms.openlocfilehash: 26aa54dc19222f7dd15dd5e64db939abdc8fada3
ms.sourcegitcommit: bd43c7961e93ef200b92fb1d6f09d9ad153dd082
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2022
ms.locfileid: "137907046"
---
# <a name="lab-implementing-storage-solutions-in-windows-server"></a>랩: Windows Server에서 스토리지 솔루션 구현

## <a name="scenario"></a>시나리오

Contoso, Ltd.에서는 스토리지 액세스를 간소화하고 스토리지 수준에서 중복성을 제공하기 위해 Windows Server 서버에서 스토리지 공간 기능을 구현해야 합니다. 경영진은 스토리지를 절약하기 위해 데이터 중복 제거를 테스트하도록 요청했습니다. 또한 iSCSI(Internet Small Computer System Interface) 스토리지를 구현하여 조직에서 스토리지를 배포하는 데 더 간단한 솔루션을 제공하기를 원합니다. 이 밖에도 조직은 스토리지를 고가용성으로 만들고 고가용성을 위해 충족해야 하는 요구 사항을 조사하는 옵션을 모색하고 있습니다. 특히 스토리지 공간 다이렉트에서 고가용성 스토리지를 사용할 수 있는지 테스트하려고 합니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음을 수행할 수 있습니다.

- 데이터 중복 제거를 구현합니다.
- iSCSI 스토리지를 구성합니다.
- 스토리지 공간을 구성합니다.
- 스토리지 공간 다이렉트를 구현합니다.

## <a name="estimated-time-90-minutes"></a>예상 시간: 90분

## <a name="lab-setup"></a>랩 설정

가상 머신: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, **AZ-800T00A-SEA-SVR2**, **AZ-800T00A-SEA-SVR3** 및 **AZ-800T00A-ADM1** 이 실행 중이어야 합니다. 

- 연습 1~3: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR3** 및 **AZ-800T00A-SEA-ADM1**
- 연습 4: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, **AZ-800T00A-SEA-SVR2**, **AZ-800T00A-SEA-SVR3** 및 **AZ-800T00A-SEA-ADM1**

> **참고**: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, **AZ-800T00A-SEA-SVR2**, **AZ-800T00A-SEA-SVR3** 및 **AZ-800T00A-SEA-ADM1** 가상 머신은 각각 **SEA-DC1**, **SEA-SVR1**, **SEA-SVR2**, **SEA-SVR3** 및 **SEA-ADM1** 의 설치를 호스트합니다.

1. **SEA-ADM1** 을 선택합니다.
1. 다음 자격 증명을 사용하여 로그인합니다.

   - 사용자 이름: **Administrator**
   - 암호: **Pa55w.rd**
   - 도메인: **CONTOSO**

이 랩에서는 사용 가능한 VM 환경을 사용합니다.

## <a name="lab-exercise-1-implementing-data-deduplication"></a>랩 연습 1: 데이터 중복 제거 구현

### <a name="scenario"></a>시나리오

서버 관리자를 사용하여 데이터 중복 제거 역할 서비스를 설치하기로 결정했습니다. **M** 드라이브가 많이 사용되고 있음을 확인하고 일부 폴더에 중복 파일이 포함되어 있다고 의심합니다. 이 볼륨에서 사용된 공간을 줄이기 위해 데이터 중복 제거 역할을 사용하도록 설정하고 구성하기로 결정합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. **SEA-SVR3** 에 데이터 중복 제거 기능을 설치합니다.
1. **SEA-SVR3** 의 **M** 드라이브에서 데이터 중복 제거를 사용하도록 설정하고 구성합니다.
1. 파일을 추가하고 중복 제거를 관찰하여 데이터 중복 제거를 테스트합니다.

#### <a name="task-1-install-the-data-deduplication-role-service"></a>작업 1: 데이터 중복 제거 역할 서비스 설치

1. **SEA-ADM1** 에서 **서버 관리자** 를 사용하여 **SEA-SVR3** 에 데이터 중복 제거 역할 서비스를 설치합니다.
1. **SEA-ADM1** 에서 **C:\Labfiles** 폴더를 **사용자** 그룹에 부여된 **읽기** 권한과 공유합니다.
1. **SEA-SVR3** 콘솔 세션으로 전환한 다음 필요한 경우 **Pa55w.rd** 암호로 **CONTOSO\\Administrator** 로 로그인합니다.
1. **SEA-SVR3** 에서 **Windows PowerShell** 세션을 시작한 다음 **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 ReFS로 포맷되고 **M** 드라이브 문자가 할당된 볼륨을 만듭니다.

   ```powershell
   Get-Disk
   Initialize-Disk -Number 1
   New-Partition -DiskNumber 1 -UseMaximumSize -DriveLetter M
   Format-Volume -DriveLetter M -FileSystem ReFS
   ```
1. **SEA-SVR3** 의 **Windows PowerShell** 콘솔에서 다음 명령을 입력하여 **SEA-ADM1** 에서 중복 제거할 샘플 파일을 만들고, 실행하고, 결과를 식별하는 스크립트를 복사합니다.

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

1. **SEA-ADM1** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **서버 관리자** 의 **파일 및 스토리지 서비스** 인터페이스를 사용하여 **SEA-SVR3** 의 디스크를 표시합니다.
1. 다음 설정을 사용하여 **SEA-SVR3** 의 디스크 번호 **1** 에 있는 **M** 볼륨에서 데이터 중복 제거를 사용하도록 설정합니다.

   - 중복 제거 옵션: **범용 파일 서버**
   - 다음보다 오래된 파일 중복 제거(일): **0**
   - 처리량 최적화 사용: 선택됨

#### <a name="task-3-test-data-deduplication"></a>작업 3: 데이터 중복 제거 테스트

1. **SEA-ADM1** 에서 관리자 권한으로 **Windows PowerShell** 을 시작합니다.

   >**참고**: **SEA-ADM1** 에 Windows Admin Center를 아직 설치하지 않았다면 다음 두 단계를 수행합니다.

1. **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 Windows Admin Center 최신 버전을 다운로드합니다.
    
   ```powershell
   Start-BitsTransfer -Source https://aka.ms/WACDownload -Destination "$env:USERPROFILE\Downloads\WindowsAdminCenter.msi"
   ```
1. 다음 명령을 실행하여 Windows Admin Center를 설치합니다.
    
   ```powershell
   Start-Process msiexec.exe -Wait -ArgumentList "/i $env:USERPROFILE\Downloads\WindowsAdminCenter.msi /qn /L*v log.txt REGISTRY_REDIRECT_PORT_80=1 SME_PORT=443 SSL_CERTIFICATE_OPTION=generate"
   ```

   > **참고**: 설치가 완료될 때까지 기다리세요. 이 작업은 2분 정도 걸립니다.

1. **SEA-ADM1** 에서 Microsoft Edge 시작하고 `https://SEA-ADM1.contoso.com`에 있는 Windows Admin Center의 로컬 인스턴스에 연결합니다. 
1. 메시지가 표시되면 **Windows 보안** 대화 상자에 다음 자격 증명을 입력한 다음 **확인** 을 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. Windows Admin Center에서 **sea-svr3.contoso.com** 에 연결을 추가하고 **Pa55w.rd** 암호를 사용하여 **CONTOSO\\Administrator** 자격으로 연결합니다.
1. **sea-svr3.contoso.com** 에 연결된 상태에서 **도구** 목록의 **PowerShell** 도구를 사용하여 중복 제거를 트리거하는 다음 명령을 실행합니다.

   ```powershell
   Start-DedupJob -Volume M: -Type Optimization –Memory 50
   ```
1. **SEA-SVR3** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **SEA-SVR3** 의 **Windows PowerShell** 프롬프트에서 다음 명령을 실행하여 중복 제거되는 볼륨에서 사용 가능한 공간을 식별합니다.

   ```powershell
   Get-PSDrive -Name M
   ```

   > **참고**: 이전에 표시된 값을 현재 값과 비교합니다. 

1. 중복 제거 작업이 완료되고 이전 단계를 반복할 수 있도록 5~10분 정도 기다립니다.
1. **SEA-ADM1** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **SEA-ADM1** 의 **sea-svr3.contoso.com** 에 연결된 Windows Admin Center에서 **PowerShell** 도구를 사용하여 중복 제거 작업의 상태를 식별하는 다음 명령을 실행합니다.

   ```powershell
   Get-DedupStatus –Volume M: | fl
   Get-DedupVolume –Volume M: |fl
   Get-DedupMetadata –Volume M: |fl
   ```
1. **SEA-ADM1** 의 **서버 관리자** 에서 디스크 창을 새로 고쳐 **M:** 볼륨의 속성을 표시합니다.
1. **볼륨(M:\) 중복 제거 속성** 창에서 **중복 제거 비율** 및 **중복 제거 절감** 에 대한 값을 검토합니다.

## <a name="lab-exercise-2-configuring-iscsi-storage"></a>랩 연습 2: iSCSI 스토리지 구성

### <a name="scenario"></a>시나리오

Contoso의 경영진은 iSCSI를 사용하여 중앙 집중식 스토리지를 구성하는 비용 및 복잡성을 줄이는 옵션을 모색하고 있습니다. 이를 테스트하기 위해 iSCSI 대상을 설치 및 구성하고 대상에 대한 액세스를 제공하도록 iSCSI 초기자를 구성해야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. **SEA-SVR3** 에 iSCSI를 설치하고 대상을 구성합니다.
1. **SEA-DC1**(초기자)에서 iSCSI 대상을 연결하고 구성합니다.
1. iSCSI 디스크 구성을 확인합니다.
1. 디스크 구성을 되돌립니다.

#### <a name="task-1-install-iscsi-and-configure-targets"></a>작업 1: iSCSI 설치 및 대상 구성

1. **SEA-ADM1** 의 **Windows PowerShell** 콘솔에서 다음 명령을 입력하여 **SEA-SVR3** 에 대한 PowerShell 원격 세션을 설정합니다.

   ```powershell
   Enter-PSSession -ComputerName SEA-SVR3
   ```
1. 다음 명령을 실행하여 **SEA-SVR3** 에 iSCSI 대상을 설치합니다.

   ```powershell
   Install-WindowsFeature –Name FS-iSCSITarget-Server –IncludeManagementTools
   ```
1. 다음 명령을 실행하여 디스크 2에서 ReFS로 포맷된 새 볼륨을 만듭니다.

   ```powershell
   Initialize-Disk -Number 2
   $partition2 = New-Partition -DiskNumber 2 -UseMaximumSize -AssignDriveLetter
   Format-Volume -DriveLetter $partition2.DriveLetter -FileSystem ReFS
   ```
1. 다음 명령을 실행하여 디스크 3에서 ReFS로 포맷된 새 볼륨을 만듭니다.

   ```powershell
   Initialize-Disk -Number 3
   $partition3 = New-Partition -DiskNumber 3 -UseMaximumSize -AssignDriveLetter
   Format-Volume -DriveLetter $partition3.DriveLetter -FileSystem ReFS
   ```
1. 다음 명령을 실행하여 iSCSI 트래픽을 허용하는 고급 보안 규칙으로 Windows Defender 방화벽을 구성합니다.

   ```powershell
   New-NetFirewallRule -DisplayName "iSCSITargetIn" -Profile "Any" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 3260
   New-NetFirewallRule -DisplayName "iSCSITargetOut" -Profile "Any" -Direction Outbound -Action Allow -Protocol TCP -LocalPort 3260
   ```
1. 다음 명령을 실행하여 새로 만든 볼륨에 할당된 드라이브 문자를 표시합니다.

   ```powershell
   $partition2.DriveLetter
   $partition3.DriveLetter
   ```

   > **참고**: 지침에서는 드라이브 문자가 각각 **E** 및 **F** 라고 가정합니다. 드라이브 문자 할당이 다른 경우, 이 연습의 지침에 따르는 동안 이 점을 고려합니다.

#### <a name="task-2-connect-to-and-configure-iscsi-targets"></a>작업 2: iSCSI 대상 연결 및 구성

1. **SEA-ADM1** 의 **서버 관리자** 에서 디스크 창을 새로 고쳐 **SEA-DC1** 의 디스크 구성을 표시합니다. 부팅 및 시스템 볼륨 드라이브 **C** 만 포함된 것을 알 수 있습니다.
1. **서버 관리자** 의 **파일 및 스토리지 서비스** 에서 iSCSI 창으로 전환합니다. 
1. iSCSI 창에서 다음 설정을 사용하여 iSCSI 가상 디스크를 만듭니다.

   - 스토리지 위치: **E:**
   - 이름: **iSCSIDisk1**
   - 디스크 크기: **5GB**, **동적 확장**
   - iSCSI 대상: **새로 만들기**
   - 대상 이름: **iSCSIFarm**
   - 액세스 서버: **SEA-DC1**

1. 다음 설정을 사용하여 두 번째 iSCSI 가상 디스크를 만듭니다.

   - 스토리지 위치: **F:**
   - 이름: **iSCSIDisk2**
   - 디스크 크기: **5GB**, **동적 확장**
   - iSCSI 대상: **iSCSIFarm**

1. **SEA-DC1** 콘솔 세션으로 전환한 다음 필요한 경우 **Pa55w.rd** 암호를 사용하여 **CONTOSO\\Administrator** 로 로그인합니다.
1. **SEA-SVR3** 에서 **Windows PowerShell** 세션을 시작한 다음 **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 iSCSI 초기자 서비스를 시작하고 iSCSI 초기자 구성을 표시합니다.

   ```powershell
   Start-Service msiscsi
   iscsicpl
   ```

   > **참고**: **iscsicpl** 명령은 **iSCSI 초기자 속성** 창을 엽니다.

1. **iSCSI 초기자 속성** 인터페이스를 사용하여 다음 iSCSI 대상에 연결합니다.

   - 이름: **SEA-SVR3**
   - 대상 이름: **iqn.1991-05.com.microsoft:SEA-SVR3-fileserver-target**

#### <a name="task-3-verify-iscsi-disk-configuration"></a>작업 3: iSCSI 디스크 구성 확인 

1. **SEA-ADM1** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **서버 관리자** 에서 **파일 및 스토리지 서비스** 의 디스크 창으로 이동하여 해당 보기를 새로 고칩니다. 
1. **SEA-DC1** 디스크 구성을 검토하고 **오프라인** 상태에서 **iSCSI** 버스 유형인 **5 GB** 디스크를 두 개 포함하고 있는지 확인합니다.
1. **SEA-DC1** 콘솔 세션으로 전환합니다. 
1. **Windows PowerShell** 프롬프트에서 다음 명령을 실행하여 디스크 구성을 표시합니다.

   ```powershell
   Get-Disk
   ```
   > **참고**: 두 디스크가 모두 존재하고 정상적이지만 오프라인 상태입니다. 이 두 디스크를 사용하려면 초기화하고 포맷해야 합니다.

1. **SEA-DC1** 의 **Windows PowerShell** 프롬프트에서 다음 명령을 실행하여 ReFS로 포맷되고 드라이브 문자 **E** 가 할당된 볼륨을 만듭니다.

   ```powershell
   Initialize-Disk -Number 1
   New-Partition -DiskNumber 1 -UseMaximumSize -DriveLetter E
   Format-Volume -DriveLetter E -FileSystem ReFS
   ```
1. 이전 단계를 반복하여 ReFS로 포맷된 새 드라이브를 만들되 이번에는 디스크 번호 **2** 와 드라이브 문자 **F** 를 사용합니다.
1. **서버 관리자** 창이 활성화된 상태에서 **SEA-ADM1** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **서버 관리자** 의 **파일 및 스토리지 서비스** 에서 디스크 창을 새로 고칩니다.
1. **SEA-DC1** 디스크 구성을 검토하고 두 드라이브 모두 이제 **온라인** 상태인지 확인합니다.

#### <a name="task-4-revert-disk-configuration"></a>작업 4: 디스크 구성 되돌리기 

1. **SEA-SVR3** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **Windows PowerShell** 프롬프트에서 다음 명령을 실행하여 **SEA-SVR3** 의 디스크를 원래 상태로 다시 설정합니다.

   ```powershell
   for ($num = 1;$num -le 4; $num++) {Clear-Disk -Number $num -RemoveData -RemoveOEM -ErrorAction SilentlyContinue}
   for ($num = 1;$num -le 4; $num++) {Set-Disk -Number $num -IsOffline $true}
   ```

   > **참고**: 다음 연습을 준비하려면 이 작업이 필요합니다.

## <a name="lab-exercise-3-configuring-redundant-storage-spaces"></a>랩 연습 3: 중복 스토리지 공간 구성

### <a name="scenario"></a>시나리오

고가용성에 대한 몇 가지 요구 사항을 충족하기 위해 스토리지 공간 중복성 옵션을 평가하기로 결정했습니다. 또한 스토리지 풀에 대한 새 디스크 프로비전을 테스트하려고 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. 스토리지 풀을 생성합니다.
1. 3방향 미러 디스크를 기반으로 볼륨을 만듭니다.
1. 파일 탐색기에서 볼륨을 관리합니다.
1. 스토리지 풀에서 디스크 연결을 끊고 볼륨 가용성을 확인합니다.
1. 스토리지 풀에 디스크를 추가하고 볼륨 가용성을 확인합니다.
1. 디스크 구성을 되돌립니다.

> **참고:** Windows Server에서는 스토리지 풀의 디스크 연결을 끊을 수 없습니다. 제거만 가능합니다. 또한 새 디스크를 먼저 추가하지 않으면 3방향 미러에서 디스크를 제거할 수 없습니다. 

#### <a name="task-1-create-a-storage-pool"></a>작업 1: 스토리지 풀 만들기 

1. **SEA-ADM1** 로 전환하고 **서버 관리자** 의 **파일 및 스토리지 서비스** 에서 디스크 창을 새로 고칩니다.
1. **SEA-SVR3** 에서 디스크 1~4의 상태를 **온라인** 으로 설정합니다.
1. **SEA-SVR3** 의 스토리지 구성을 대상으로 하는 **서버 관리자** 에서 크기가 **127GB** 인 3개의 디스크로 구성된 **SP1** 이라는 새 스토리지 풀을 만듭니다.

#### <a name="task-2-create-a-volume-based-on-a-three-way-mirrored-disk"></a>작업 2: 3방향 미러 디스크를 기반으로 볼륨 만들기 

1. **SEA-ADM1** 의 **서버 관리자** 에서 새로 만든 스토리지 풀 **SP1** 을 사용하여 미러 스토리지 레이아웃, 씬 프로비저닝을 사용하고 크기가 **25GB** 인 **Three-Mirror** 라는 가상 디스크를 만듭니다.
1. 새로 프로비전된 가상 디스크를 사용하여 **TestData** 라는 ReFS 볼륨을 만들고, 크기를 사용 가능한 모든 디스크 공간으로 설정하고, 드라이브 문자 **T** 를 할당합니다.

#### <a name="task-3-manage-a-volume-in-file-explorer"></a>작업 3: 파일 탐색기에서 볼륨 관리 

1. **SEA-ADM1** 에서, **SEA-SVR3** 에 PowerShell 원격 세션을 호스팅하는 **Windows PowerShell** 로 전환합니다.
1. PowerShell 원격 세션에서 다음 명령을 실행하여 고급 보안으로 Windows Defender 방화벽의 모든 파일 및 프린터 공유 규칙을 사용하도록 설정합니다.

   ```
   Enable-NetFirewallRule -Group "@FirewallAPI.dll,-28502"
   ```

1. **SEA-ADM1** 에서 **파일 탐색기** 를 시작하고 **\\\\SEA-SVR3.contoso.com\\t$** 공유로 이동합니다.
1. **TestData** 라는 폴더를 만든 다음 이 폴더 내에 **TestDocument.txt** 라는 문서를 만듭니다.

#### <a name="task-4-disconnect-a-disk-from-the-storage-pool-and-verify-volume-availability"></a>작업 4: 스토리지 풀에서 디스크 연결 끊기 및 볼륨 가용성 확인 

1. **SEA-ADM1** 에서 **서버 관리자** 를 사용하여 **SEA-SVR3** 에 연결된 나머지 사용 가능한 디스크를 스토리지 풀 **SP1** 에 추가합니다. 디스크에서 자동 할당을 사용하는지 확인합니다.
1. **서버 관리자** 를 사용하여 **SP1** 풀에 할당된 처음 세 개의 디스크 중 하나를 제거합니다.
1. **SEA-ADM1** 에서 파일 탐색기를 사용하여 **TestDocument.txt** 를 계속 사용할 수 있는지 확인합니다. 

#### <a name="task-5-add-a-disk-to-the-storage-pool-and-verify-volume-availability"></a>작업 5: 스토리지 풀에 디스크 추가 및 볼륨 가용성 확인 

1. **SEA-ADM1** 의 **서버 관리자** 에서 **SP1** 스토리지 풀을 다시 검색합니다.
1. 이전 작업에서 제거한 디스크를 다시 추가하고 자동 할당을 사용하는지 확인합니다.
1. **SEA-ADM1** 에서 파일 탐색기를 사용하여 **TestDocument.txt** 를 계속 사용할 수 있는지 확인합니다. 
1. **SEA-SVR3** 으로 다시 전환합니다.

#### <a name="task-6-revert-disk-configuration"></a>작업 6: 디스크 구성 되돌리기 

1. **SEA-SVR3** 에 대한 콘솔 세션으로 다시 전환합니다.
1. **Windows PowerShell** 프롬프트에서 다음 명령을 실행하여 **SEA-SVR3** 의 디스크를 원래 상태로 다시 설정합니다.

   ```powershell
   Get-VirtualDisk -FriendlyName 'Three-Mirror' | Remove-VirtualDisk
   Get-StoragePool -FriendlyName 'SP1' | Remove-StoragePool
   for ($num = 1;$num -le 4; $num++) {Clear-Disk -Number $num -RemoveData -RemoveOEM -ErrorAction SilentlyContinue}
   for ($num = 1;$num -le 4; $num++) {Set-Disk -Number $num -IsOffline $true}
   ```

   > **참고**: 다음 연습을 준비하려면 이 작업이 필요합니다.

## <a name="lab-exercise-4-implementing-storage-spaces-direct"></a>랩 연습 4: 스토리지 공간 다이렉트 구현

### <a name="scenario"></a>시나리오

로컬 스토리지를 고가용성 스토리지로 사용하는 것이 조직에 적합한 솔루션인지 테스트하려고 합니다. 이전에는 조직에서 VM을 저장하기 위해 SAN(스토리지 영역 네트워크)만 사용했습니다. Windows Server의 기능을 사용하면 로컬 스토리지만 사용하도록 할 수 있으므로 테스트 구현으로 스토리지 공간 다이렉트를 구현하려고 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. 스토리지 공간 다이렉트 설치를 준비합니다.
1. 장애 조치(failover) 클러스터를 만들고 유효성을 검사합니다.
1. 스토리지 공간을 직접 사용하도록 설정합니다.
1. 스토리지 풀, 가상 디스크 및 공유를 만듭니다.
1. 스토리지 공간 다이렉트 기능을 확인합니다.

#### <a name="task-1-prepare-for-installation-of-storage-spaces-direct"></a>작업 1: 스토리지 공간 다이렉트 설치 준비 

1. **SEA-ADM1** 에서, 계속하기 전에 **서버 관리자** 에서 **SEA-SVR1**, **SEA-SVR2** 및 **SEA-SVR3** 의 **관리 가능성** 상태가 **온라인 - 성능 카운터가 시작되지 않음** 인지 확인합니다.
1. **서버 관리자** 에서 **파일 및 스토리지 서비스** 의 디스크 창으로 이동합니다.
1. 디스크 창에서 **SEA-SVR3** 노드로 이동하여 디스크 1~4가 **알 수 없음** 으로 나열되었는지 확인합니다.
1. **서버 관리자** 의 디스크 창에서 **SEA-SVR1**, **SEA-SVR2** 및 **SEA-SVR3** 에 연결된 모든 디스크를 온라인으로 설정합니다.
1. **SEA-ADM1** 에서 **Windows PowerShell ISE** 를 시작하고 스크립트 창에서 **C:\\Labfiles\\Lab09\\Implement-StorageSpacesDirect.ps1** 을 엽니다.

   > **참고**: 스크립트는 번호가 매겨진 단계로 나뉩니다. 8단계가 있으며 각 단계마다 여러 명령이 있습니다. 개별 줄을 실행하려면 해당 줄 내의 아무 곳에나 커서를 놓고 F8 키를 누르거나 **Windows PowerShell ISE** 창의 도구 모음에서 **선택 항목 실행** 을 선택할 수 있습니다. 여러 줄을 실행하려면 전체 줄을 모두 선택한 다음 F8 또는 **선택 항목 실행** 도구 모음 아이콘을 사용합니다. 단계의 순서는 이 연습의 지침에 설명되어 있습니다. 다음 단계를 시작하기 전에 각 단계가 완료되었는지 확인합니다.

1. 1단계에서 첫 번째 명령을 실행하여 **SEA-SVR1**, **SEA-SVR2** 및 **SEA-SVR3** 에 파일 서버 역할 및 장애 조치(failover) 클러스터링 기능을 설치합니다.

   > **참고**: 설치가 끝날 때까지 기다리세요. 이 작업은 2분 정도 걸립니다. 각 명령의 출력에서 **Success** 속성이 **True** 로 설정되어 있는지 확인합니다.

1. 1단계에서 두 번째 명령을 실행하여 **SEA-SVR1**, **SEA-SVR2** 및 **SEA-SVR3** 을 다시 시작합니다.

   > **참고**: 두 번째 명령을 호출하여 서버를 다시 시작하면, 세 번째 명령을 실행하여 다시 시작이 완료되기를 기다리지 않고 장애 조치(failover) 클러스터링 관리 도구를 설치할 수 있습니다.

1. 1단계에서 세 번째 명령을 실행하여 **SEA-ADM1** 에 **장애 조치(failover) 클러스터 관리자** 도구를 설치합니다.

   > **참고**: 서버를 다시 시작하고 **장애 조치(failover) 클러스터 관리자** 도구가 **SEA-ADM1** 에 설치되는 동안 몇 분 정도 기다립니다.

#### <a name="task-2-create-and-validate-the-failover-cluster"></a>작업 2: 장애 조치(failover) 클러스터 만들기 및 유효성 검사 

1. **SEA-ADM1** 에서 **장애 조치(failover) 클러스터 관리자** 콘솔을 시작합니다.
1. **SEA-ADM1** 의 **Windows PowerShell ISE** 에서 2단계 명령을 실행하여 클러스터 유효성 검사 테스트를 호출합니다.

   > **참고**: 테스트가 완료될 때까지 기다리세요. 이 작업은 2분 정도 걸립니다. 실패한 테스트가 없는지 확인합니다. 이러한 경고는 예상되기 때문에 무시합니다.

1. **Windows PowerShell ISE** 에서 3단계 명령을 실행하여 클러스터를 만듭니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이 작업은 2분 정도 걸립니다. 

1. 명령이 완료되면 **장애 조치(failover) 클러스터 관리자** 로 전환하고 **S2DCluster.Contoso.com** 이라는 새로 만든 클러스터를 추가합니다.

#### <a name="task-3-enable-storage-spaces-direct"></a>작업 3: 스토리지 공간 다이렉트 사용 설정

1. **SEA-ADM1** 의 **Windows PowerShell ISE** 에서 4단계 명령을 실행하여 새로 설치한 클러스터에서 스토리지 공간 다이렉트를 사용하도록 설정합니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이 작업은 1분 정도 걸립니다.

1. **Windows PowerShell ISE** 에서 5단계 명령을 실행하여 **S2DStoragePool** 이라는 스토리지 풀을 만듭니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이는 1분 이내에 완료됩니다. 명령의 출력에서 **FriendlyName** 특성의 값이 **S2DStoragePool** 인지 확인합니다.

1. **장애 조치(failover) 클러스터 관리자** 로 전환하고 클러스터에 **Cluster Pool 1** 이라는 스토리지 풀이 포함되어 있는지 확인합니다.
1. **Windows PowerShell ISE** 로 전환하고 6단계 명령을 실행하여 가상 디스크를 만듭니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이는 1분 이내에 완료됩니다. 

1. **장애 조치(failover) 클러스터 관리자** 로 전환하고 디스크 창에 **CSV(클러스터 가상 디스크)** 개체가 표시되는지 확인합니다.

#### <a name="task-4-create-a-storage-pool-a-virtual-disk-and-a-share"></a>작업 4: 스토리지 풀, 가상 디스크 및 공유 만들기

1. **SEA-ADM1** 의 **Windows PowerShell ISE** 에서 7단계 명령을 실행하여 S2D-SOFS 역할을 만듭니다.

   > **참고**: 단계가 완료될 때까지 기다리세요. 이는 1분 이내에 완료됩니다. 

1. **장애 조치(failover) 클러스터 관리자** 로 전환하고 역할 창에 **S2D-SOFS** 개체가 표시되는지 확인합니다.
1. **Windows PowerShell ISE** 로 전환하고 8단계의 세 가지 명령을 모두 실행하여 **VM01** 공유를 만듭니다. 
1. **장애 조치(failover) 클러스터 관리자** 로 전환하고 공유 창에 **VM01** 공유가 표시되는지 확인합니다.

#### <a name="task-5-verify-storage-spaces-direct-functionality"></a>작업 5: 스토리지 공간 다이렉트 기능 확인

1. **SEA-ADM1** 의 파일 탐색기에서 **\\\\s2d-sofs\\VM01** 공유를 열고 **VMFolder** 라는 폴더를 만듭니다.
1. **SEA-ADM1** 의 **Windows PowerShell ISE** 콘솔 창에서 다음 명령을 실행하여 **SEA-SVR3** 을 종료합니다.

   ```powershell
   Stop-Computer -ComputerName SEA-SVR3 -Force
   ```
1. **서버 관리자** 로 전환하고 **모든 서버** 보기를 새로 고쳐 **SEA-SVR3** 에 더 이상 액세스할 수 없는지 확인합니다.
1. **장애 조치(failover) 클러스터 관리자** 로 전환하고 **디스크** 노드에서 **CSV(클러스터 가상 디스크)** 정보를 검토합니다. 
1. **CSV(클러스터 가상 디스크)** 의 경우 **정상 상태** 가 **경고** 로 설정되고 **작동 상태** 가 **성능 저하됨** 으로 설정됩니다(**작동 상태** 는 **완료되지 않음** 으로 표시될 수도 있음).
1. **SEA-ADM1** 에서 Windows Admin Center가 표시된 Microsoft Edge 창으로 전환합니다. 
1. Windows Admin Center에서 **S2DCluster.Contoso.com** 클러스터에 연결을 추가합니다. 

   > **참고**: 클러스터 노드는 Windows Admin Center에서 이미 사용할 수 있으므로 추가하지 마세요. 

1. Windows Admin Center의 클러스터 대시보드 창에서 **SEA-SVR3** 에 연결할 수 없음을 나타내는 경고를 확인합니다. 
1. **SEA-SVR3** 에 대한 콘솔 세션으로 전환한 다음 시작합니다. 
1. 몇 분 후에 경고가 자동으로 제거되는지 확인합니다.
1. Windows Admin Center가 표시된 브라우저 페이지를 새로 고치고 모든 서버가 정상인지 확인합니다.

### <a name="results"></a>결과

이 랩을 완료하면 다음을 수행합니다.

- 데이터 중복 제거의 구현을 테스트
- iSCSI 스토리지를 설치 및 구성
- 중복 스토리지 공간을 구성
- 스토리지 공간 다이렉트의 구현을 테스트
