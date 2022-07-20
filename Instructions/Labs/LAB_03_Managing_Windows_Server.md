---
lab:
  title: '랩: Windows Server 관리'
  module: 'Module 3: Windows Server administration'
ms.openlocfilehash: 88b5dda91ee1aa239f87b94e55ed5bd6f42aca10
ms.sourcegitcommit: d34dce53481b0263d0ff82913b3f49cb173d5c06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2022
ms.locfileid: "147039397"
---
# <a name="lab-managing-windows-server"></a>랩: Windows Server 관리

## <a name="scenario"></a>시나리오

Contoso, Ltd.는 자사 환경에서 여러 개의 새 서버를 구현하려고 하며 Server Core를 사용하기로 결정했습니다. 또한 이러한 서버와 조직의 다른 서버 모두 원격으로 관리하기 위해 Windows Admin Center를 구현하려고 합니다.

## <a name="objectives"></a>목표

- Windows Admin Center 구현 및 구성

## <a name="estimated-time-45-minutes"></a>예상 시간: 45분

## <a name="lab-setup"></a>랩 설정

가상 머신: **AZ-800T00A-SEA-DC1** 및 **AZ-800T00A-ADM1** 을 실행해야 합니다. 다른 VM을 실행할 수도 있지만 이 랩에서는 필요하지 않습니다.

> **참고**: **AZ-800T00A-SEA-DC1** 및 **AZ-800T00A-SEA-ADM1** 가상 머신은 **SEA-DC1** 및 **SEA-ADM1** 의 설치를 호스트합니다.

1. **SEA-ADM1** 을 선택합니다.
1. 다음 자격 증명을 사용하여 로그인합니다.

   - 사용자 이름: **Administrator**
   - 암호: **Pa55w.rd**
   - 도메인: **CONTOSO**

이 랩에서는 사용 가능한 VM 환경과 Azure AD 테넌트를 사용합니다. 

## <a name="exercise-1-implementing-and-using-remote-server-administration"></a>연습 1: 원격 서버 관리 구현 및 사용

### <a name="scenario"></a>시나리오 

이제 Server Core 서버를 배포했으므로 원격 관리를 위해 Windows Admin Center를 구현해야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Windows Admin Center 설치
1. 원격 관리를 위한 서버 추가
1. Windows Admin Center 확장 구성
1. 원격 관리 확인
1. 원격 PowerShell을 사용하여 서버 관리

#### <a name="task-1-install-windows-admin-center"></a>작업 1: Windows Admin Center 설치

1. **SEA-ADM1** 에서 관리자 권한으로 **Windows PowerShell** 을 시작합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 Windows Admin Center 최신 버전을 다운로드합니다.
    
   ```powershell
   Start-BitsTransfer -Source https://aka.ms/WACDownload -Destination "$env:USERPROFILE\Downloads\WindowsAdminCenter.msi"
   ```
1. 다음 명령을 입력한 다음 Enter 키를 눌러 Windows Admin Center를 설치합니다.
    
   ```powershell
   Start-Process msiexec.exe -Wait -ArgumentList "/i $env:USERPROFILE\Downloads\WindowsAdminCenter.msi /qn /L*v log.txt REGISTRY_REDIRECT_PORT_80=1 SME_PORT=443 SSL_CERTIFICATE_OPTION=generate"
   ```

   > **참고**: 설치가 완료될 때까지 기다리세요. 이 작업은 2분 정도 걸립니다.

   > **참고**: 설치가 완료되면 ‘ERR_Connection_Refused’ 오류 메시지가 표시될 수 있습니다. 이 경우 SEA-ADM1을 다시 시작하여 문제를 해결합니다.

#### <a name="task-2-add-servers-for-remote-administration"></a>작업 2: 원격 관리를 위한 서버 추가

1. **SEA-ADM1** 에서 Microsoft Edge를 시작한 다음 `https://SEA-ADM1.contoso.com`으로 이동합니다. 
1. 메시지가 표시되면 **CONTOSO\\Administrator** 사용자 이름과 **Pa55w.rd** 암호로 로그인합니다.
1. **모든 연결** 페이지를 검토합니다. **sea-adm1.contoso.com** 항목이 포함되어 있는 것을 알 수 있습니다. 
1. 모든 연결 창에서 `sea-dc1.contoso.com`에 대한 연결을 추가합니다.
1. 메시지가 표시되면 **CONTOSO\\Administrator** 사용자 이름과 **Pa55w.rd** 암호로 로그인합니다.

   > **참고**: Single Sign-On을 수행하려면 Kerberos 제한 위임을 설정해야 합니다.

#### <a name="task-3-configure-windows-admin-center-extensions"></a>작업 3: Windows Admin Center 확장 구성

1. **SEA-ADM1** 의 오른쪽 상단에서 **설정** 아이콘(톱니바퀴)을 선택합니다.
1. 사용 가능한 확장을 검토합니다.
1. **보안(미리 보기)** 확장을 설치합니다. 확장이 설치되고 Windows Admin Center가 새로 고쳐집니다.

   > **참고**: **보안(미리 보기)** 확장을 사용할 수 없는 경우 다른 Microsoft 확장을 선택합니다.

1. 설치된 확장 목록에 DNS(미리 보기) 확장이 포함되어 있는지 확인합니다.
1. 상단 메뉴의 **설정** 옆에 있는 드롭다운 화살표를 선택한 다음 **서버 관리자** 를 선택합니다.
1. Windows Admin Center 내에서 `sea-dc1.contoso.com`에 연결하고 필요한 경우 **CONTOSO\\Administrator** 사용자 이름과 **Pa55w.rd** 암호로 로그인합니다.
1. `sea-dc1.contoso.com`에서 DNS 서버에 연결하고 DNS PowerShell 도구를 설치합니다.
1. **Contoso.com** 영역을 선택하고 해당 DNS 레코드 목록을 검토합니다.

#### <a name="task-4-verify-remote-administration"></a>작업 4: 원격 관리 확인

1. **SEA-ADM1** 의 Windows Admin Center에서 `sea-dc1.contoso.com`에 연결하면서 개요 창을 검토합니다. Windows Admin Center의 세부 정보 창에는 기본 서버 정보 및 성능 모니터링이 표시됩니다.
1. Windows Admin Center에서 역할 및 기능 도구를 사용하여 `sea-dc1.contoso.com`에 **텔넷 클라이언트** 를 설치합니다. 
1. Windows Admin Center에서 **설정** 인터페이스를 사용하여 `sea-dc1.contoso.com`에서 원격 데스크톱을 활성화합니다.
1. Windows Admin Center에서 원격 데스크톱을 통해 `sea-dc1.contoso.com`에 연결합니다.
1. 원격 데스크톱 세션에서 연결을 끊습니다. 
1. Microsoft Edge 창을 닫습니다.

#### <a name="task-5-administer-servers-with-remote-powershell"></a>작업 5: 원격 PowerShell을 사용하여 서버 관리

1. **SEA-ADM1** 에서 **Windows PowerShell** 콘솔로 전환합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 **SEA-DC1** 에 대한 PowerShell Remoting 세션을 시작합니다.

   ```powershell
   Enter-PSSession -ComputerName SEA-DC1
   ```
1. **[SEA-DC1]** 프롬프트에서 다음 명령을 실행하여 AppIDSvc(애플리케이션 ID 서비스)의 상태를 표시합니다.

   ```powershell
   Get-Service -Name AppIDSvc
   ```

   > **참고**: 서비스가 현재 중지되었는지 확인합니다.

1. **[SEA-DC1]** 프롬프트에서 다음 명령을 실행하여 애플리케이션 ID 서비스를 시작합니다.

   ```powershell
   Start-Service -Name AppIDSvc
   ```
1. **[SEA-DC1]** 프롬프트에서 다음 명령을 실행하여 AppIDSvc(애플리케이션 ID 서비스)의 상태를 표시합니다.

   ```powershell
   Get-Service -Name AppIDSvc
   ```

   > **참고**: 서비스가 현재 실행 중인지 확인합니다.

### <a name="results"></a>결과

이 연습을 완료하고 나면 Windows Admin Center가 설치되고 랩 환경의 서버에 연결됩니다. 기능을 설치하고 원격 데스크톱 연결을 사용하도록 설정하고 테스트하는 등 몇 가지 원격 관리 작업을 수행했습니다. 마지막으로 PowerShell 원격을 사용하여 서비스의 상태를 확인한 다음 서비스를 시작했습니다.
