---
lab:
  title: '랩: Windows Server 관리'
  type: Answer Key
  module: 'Module 3: Windows Server administration'
ms.openlocfilehash: 2a57ebb13406a063390a232275e354c167c6e494
ms.sourcegitcommit: d34dce53481b0263d0ff82913b3f49cb173d5c06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2022
ms.locfileid: "147039445"
---
# <a name="lab-answer-key-managing-windows-server"></a>랩 답변 키: Windows Server 관리

## <a name="exercise-1-implementing-and-using-remote-server-administration"></a>연습 1: 원격 서버 관리 구현 및 사용

#### <a name="task-1-install-windows-admin-center"></a>작업 1: Windows Admin Center 설치

1. **SEA-ADM1** 에 연결하고, 필요한 경우 **Pa55w.rd** 암호를 사용해 **Contoso\\Administrator** 로 로그인합니다.
1. **SEA-ADM1** 에서 **시작** 을 선택한 다음 **Windows PowerShell(관리자)** 을 선택합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter 키를 눌러 Windows Admin Center의 최신 버전을 다운로드합니다.
    
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
1. 메시지가 표시되면 **Windows 보안** 대화 상자에 다음 자격 증명을 입력한 다음 **확인** 을 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. **이 릴리스의 새로운 기능** 팝업 창을 검토하고 오른쪽 위 모서리에서 **닫기** 를 선택합니다.
1. **모든 연결** 페이지를 검토합니다. **sea-adm1.contoso.com** 항목이 포함되어 있는 것을 알 수 있습니다. 
1. **모든 연결** 페이지에서 **+ 추가** 를 선택합니다. 
1. 리소스 추가 또는 만들기 창의 **서버** 타일에서 **추가** 를 선택합니다.
1. **서버 이름** 텍스트 상자에 **sea-dc1.contoso.com** 을 입력합니다.
1. **이 연결에 다른 계정 사용** 옵션이 선택되어 있는지 확인하고 다음 자격 증명을 입력한 다음 **자격 증명을 사용하여 추가** 를 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

   > **참고**: 7단계를 수행한 후 **연결 목록에 이 서버를 추가할 수 있지만 사용할 수 있는지 확인할 수 없다는** 오류 메시지가 나타나면 **추가** 를 선택합니다. 모든 연결 창에서 **sea-svr1.contoso.com** 을 선택하고 **다음으로 관리** 를 선택합니다. **자격 증명 지정** 대화 상자에서 **이 연결 옵션에 다른 계정 사용** 옵션이 선택되어 있는지 확인하고 관리자 자격 증명을 입력한 다음 **계속** 을 선택합니다.

   > **참고**: Single Sign-On을 수행하려면 Kerberos 제한 위임을 설정해야 합니다.

#### <a name="task-3-configure-windows-admin-center-extensions"></a>작업 3: Windows Admin Center 확장 구성

1. **SEA-ADM1** 에서, Windows Admin Center가 표시된 Microsoft Edge 창의 오른쪽 위 모서리에서 **설정** 아이콘(톱니바퀴)을 선택합니다.
1. 왼쪽 창에서 **확장** 을 선택합니다. 사용 가능한 확장을 검토합니다.
1. **보안(미리 보기)** 확장을 선택한 다음 **설치** 를 선택합니다. 확장이 설치되고 Windows Admin Center가 새로 고쳐집니다.

   > **참고**: **보안(미리 보기)** 확장을 사용할 수 없는 경우 다른 Microsoft 확장을 선택합니다.

1. 세부 정보 창에서 **설치된 확장** 을 선택하고 목록에 DNS(미리 보기) 확장이 포함되어 있는지 확인합니다.
1. 상단 메뉴의 **설정** 옆에 있는 드롭다운 화살표를 선택한 다음 **서버 관리자** 를 선택합니다.
1. **서버 연결** 페이지에서 **sea-dc1.contoso.com** 링크를 선택합니다.
1. **이 연결에 다른 계정 사용** 옵션이 선택되어 있는지 확인하고, **모든 연결에 이 자격 증명 사용** 을 선택하고, 다음 자격 증명을 입력한 다음 **계속** 을 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. DNS PowerShell 도구를 설치하려면 왼쪽 창의 **도구** 목록에서 **DNS** 를 선택한 다음 **설치** 를 선택합니다. 도구를 설치하는 데는 1분도 채 걸리지 않습니다.
1. **Contoso.com** 영역을 선택하고 해당 DNS 레코드 목록을 검토합니다.

#### <a name="task-4-verify-remote-administration"></a>작업 4: 원격 관리 확인

1. **SEA-ADM1** 에서, Windows Admin Center의 왼쪽 창에 있는 **도구** 목록에서 **개요** 를 선택합니다. Windows Admin Center의 세부 정보 창에는 기본 서버 정보 및 성능 모니터링이 표시됩니다.
1. 왼쪽 창의 **도구** 목록에서 아래로 스크롤하여 사용 가능한 기본 관리 도구를 검토합니다. **역할 및 기능** 을 선택하고 설치됨으로 나열된 역할 및 기능과 설치할 수 있는 역할 및 기능을 확인합니다. 아래로 스크롤하여 **텔넷 클라이언트** 확인란을 선택한 다음 창 맨 위에서 **+ 설치** 를 선택합니다.
1. **역할 및 기능 설치** 창에서 **예** 를 선택하고 텔넷 클라이언트가 성공적으로 설치되었음을 확인하는 메시지를 기다립니다.
1. 왼쪽 창 맨 아래에 있는 **도구** 목록 아래에서 **설정** 을 선택합니다.
1. 오른쪽의 **설정** 섹션에서 **원격 데스크톱** 을 선택합니다.
1. **원격 데스크톱** 섹션에서 **이 컴퓨터에 대한 원격 연결 허용** 확인란을 선택한 다음 **저장** 을 선택합니다.
1. 왼쪽 창의 **도구** 목록에서 **원격 데스크톱** 을 선택합니다.
1. 원격 데스크톱 창에서 **이 컴퓨터에서 제공하는 인증서를 사용하여 자동으로 연결** 확인란을 선택한 다음 **연결** 을 선택합니다.
1. 메시지가 표시되면 **확인** 을 선택한 다음 **연결** 을 선택합니다.
1. Windows Admin Center 인터페이스 내에서 원격 데스크톱을 통해 **SEA-DC1** 에 성공적으로 연결되었는지 확인합니다.
1. **연결 끊기** 를 선택합니다.
1. Microsoft Edge 창을 닫습니다.

#### <a name="task-5-administer-servers-with-remote-powershell"></a>작업 5: 원격 PowerShell을 사용하여 서버 관리

1. **SEA-ADM1** 에서 **PowerShell** 콘솔 세션으로 전환합니다. 
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter를 눌러 **SEA-DC1** 에 대한 PowerShell 원격 세션을 설정합니다.

   ```powershell
   Enter-PSSession -ComputerName SEA-DC1
   ```
1. **[SEA-DC1]** 프롬프트에서 다음 명령을 입력하고 Enter 키를 눌러 애플리케이션 ID 서비스(AppIDSvc)의 상태를 표시합니다.

   ```powershell
   Get-Service -Name AppIDSvc
   ```

   > **참고**: 서비스가 현재 중지되었는지 확인합니다.

1. **[SEA-DC1]** 프롬프트에서 다음 명령을 입력하고 Enter 키를 눌러 애플리케이션 ID 서비스를 시작합니다.

   ```powershell
   Start-Service -Name AppIDSvc
   ```
1. **[SEA-DC1]** 프롬프트에서 다음 명령을 입력하고 Enter 키를 눌러 애플리케이션 ID 서비스(AppIDSvc)의 상태를 표시합니다.

   ```powershell
   Get-Service -Name AppIDSvc
   ```

   > **참고**: 서비스가 현재 실행 중인지 확인합니다.

### <a name="results"></a>결과

이 연습을 완료하고 나면 Windows Admin Center가 설치되고 랩 환경의 서버에 연결됩니다. 기능을 설치하고 원격 데스크톱 연결을 사용하도록 설정하고 테스트하는 등 몇 가지 원격 관리 작업을 수행했습니다. 마지막으로 PowerShell 원격을 사용하여 서비스의 상태를 확인한 다음 서비스를 시작했습니다.
