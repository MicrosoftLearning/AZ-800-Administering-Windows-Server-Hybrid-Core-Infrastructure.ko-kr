---
lab:
  title: '랩: ID 서비스 및 그룹 정책 구현'
  type: Answer Key
  module: 'Module 1: Identity services in Windows Server'
ms.openlocfilehash: d68a50f3573f2817f4f83bc25cd69aba82e1d455
ms.sourcegitcommit: bd43c7961e93ef200b92fb1d6f09d9ad153dd082
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2022
ms.locfileid: "137906993"
---
# <a name="lab-answer-key-implementing-identity-services-and-group-policy"></a>랩 정답 키: ID 서비스 및 그룹 정책 구현

## <a name="exercise-1-deploying-a-new-domain-controller-on-server-core"></a>연습 1: Server Core에 새로운 도메인 컨트롤러 배포

#### <a name="task-1-deploy-ad-ds-on-a-new-windows-server-core-server"></a>작업 1: 새 Windows Server Core 서버에 AD DS 배포

1. **SEA-ADM1** 에 연결하고, 필요하면 **Pa55w.rd** 암호를 사용해 **Contoso\\Administrator** 로 로그인합니다.
1. **SEA-ADM1** 에서 **시작** 을 선택한 다음 **Windows PowerShell(관리자)** 을 선택합니다.
1. AD DS 서버 역할을 설치하려면 Windows PowerShell 명령 프롬프트에서 다음 명령을 입력한 다음 Enter 키를 누릅니다.
    
   ```powershell
   Install-WindowsFeature –Name AD-Domain-Services –ComputerName SEA-SVR1
   ```
1. AD DS 역할이 **SEA-SVR1** 에 설치되어 있는지 확인하려면 다음 명령을 입력한 다음 Enter 키를 누릅니다.
    
   ```powershell
   Get-WindowsFeature –ComputerName SEA-SVR1
   ```
1. 이전 명령의 출력에서 **Active Directory Domain Services** 확인란을 검색한 다음 선택되어 있는지 확인합니다. 그런 다음 **원격 서버 관리 도구** 를 검색합니다. 그 아래에서 **역할 관리 도구** 노드를 확인한 다음 **AD DS 및 AD LDS 도구** 노드도 선택되어 있는지 확인합니다.

   > **참고**: **AD DS 및 AD LDS 도구** 노드 아래에는 **Windows PowerShell용 Active Directory 모듈** 만 설치되어 있고 Active Directory 관리 센터와 같은 그래픽 도구는 설치되어 있지 않습니다. 서버를 중앙에서 관리하는 경우 일반적으로 각 서버에서 이러한 도구가 필요하지 않습니다. 이러한 도구를 설치하려는 경우 **RSAT-ADDS** 명령을 사용하여 **Add-WindowsFeature** cmdlet을 실행하여 AD DS 도구를 지정해야 합니다.

   > **참고**: AD DS 역할이 설치되었는지 확인하기 전에 설치 프로세스가 완료된 후 잠시 기다려야 할 수 있습니다. **Get-WindowsFeature** 명령에서 예상되는 결과가 관찰되지 않는 경우 몇 분 후에 다시 시도할 수 있습니다.

#### <a name="task-2-prepare-the-ad-ds-installation-and-promote-a-remote-server"></a>작업 2: AD DS 설치 준비 및 원격 서버 승격

1.  **SEA-ADM1** 의 **시작** 메뉴에서 **서버 관리자** 를 선택한 다음 **서버 관리자** 에서 **모든 서버** 보기를 선택합니다.
1. **관리** 메뉴에서 **서버 추가** 를 선택합니다.
1. **서버 추가** 대화 상자에서 기본 설정을 유지하고 **지금 찾기** 를 선택합니다.
1. 서버의 **Active Directory** 목록에서 **SEA-SVR1** 을 선택하고 화살표를 선택하여 **선택됨** 목록에 추가한 다음 **확인** 을 선택합니다.
1. **SEA-ADM1** 에서 **SEA-SRV1** 에 대한 AD DS 역할 설치가 완료되고 **서버 관리자** 에 서버가 추가되었는지 확인합니다. 그런 다음 **알림** 플래그 기호를 선택합니다.
1. **SEA-SVR1** 의 배포 후 구성을 확인하고 **이 서버를 도메인 컨트롤러로 승격** 링크를 선택합니다.
1. **Active Directory Domain Services 구성 마법사** 의 **배포 구성** 페이지에 있는 **배포 작업 선택** 에서 **기존 도메인에 도메인 컨트롤러 추가** 가 선택되어 있는지 확인합니다.
1. `Contoso.com` 도메인이 지정되었는지 확인하고 **이 작업을 수행하기 위한 자격 증명을 제공합니다.** 섹션에서 **변경** 을 선택합니다.
1. **배포 작업 자격 증명** 대화 상자의 **사용자 이름** 상자에 **CONTOSO\\Administrator** 를 입력하고 **암호** 상자에 **Pa55w.rd** 를 입력합니다.
1. **확인** 선택하고 **다음** 을 선택합니다.
1. **도메인 컨트롤러 옵션** 페이지에서 **DNS(Domain Name System) 서버** 및 **GC(글로벌 카탈로그)** 확인란이 선택되어 있는지 확인합니다. **RODC(읽기 전용 도메인 컨트롤러)** 확인란이 해제되어 있는지 확인합니다.
1. **DSRM(Directory Services 복원 모드) 암호 입력** 섹션에서 암호 **Pa55w.rd** 를 입력하고 확인한 후 **다음** 을 선택합니다.
1. **DNS 옵션** 페이지에서 **다음** 을 선택합니다.
1. **추가 옵션** 페이지에서 **다음** 을 선택합니다.
1. **경로** 페이지에서 **Database** 폴더, **Log files** 폴더 및 **SYSVOL** 폴더에 대한 기본 경로 설정을 유지하고 **다음** 을 선택합니다.
1. 생성된 Windows PowerShell 스크립트를 열려면 **검토 옵션** 페이지에서 **스크립트 보기** 를 선택합니다.
1. 메모장에서 생성된 Windows PowerShell 스크립트를 다음과 같이 편집합니다.

   - 숫자 기호( **#** )로 시작하는 주석 줄을 삭제합니다.
   - **Import-Module** 줄을 제거합니다.
   - 각 줄의 끝에 있는 악센트 기호( **`** )를 제거합니다.
   - 줄 바꿈을 제거합니다.

1. 이제 **Install-ADDSDomainController** 명령과 모든 매개 변수가 한 줄에 있습니다. 커서를 이 줄 앞에 놓고 **편집** 메뉴에서 **모두 선택** 을 선택하여 전체 줄을 선택합니다. 메뉴에서 **편집** 을 선택한 다음 **복사** 를 선택합니다.
1. **Active Directory Domain Services 구성 마법사** 로 전환한 다음 **취소** 를 선택합니다.
1. 확인 메시지가 표시되면 **예** 를 선택하여 마법사를 취소합니다.
1. Windows PowerShell 명령 프롬프트에서 다음 명령을 입력합니다.

   ```powershell
   Invoke-Command –ComputerName SEA-SVR1 { }
   ```
1. 중괄호( **{ }** ) 사이에 커서를 놓고 클립보드에서 복사한 스크립트 줄의 내용을 붙여넣습니다. 완료된 명령은 다음 형식이어야 합니다.
    
   ```powershell
   Invoke-Command –ComputerName SEA-SVR1 {Install-ADDSDomainController -NoGlobalCatalog:\$false -CreateDnsDelegation:\$false -Credential (Get-Credential) -CriticalReplicationOnly:\$false -DatabasePath "C:\Windows\NTDS" -DomainName "Contoso.com" -InstallDns:\$true -LogPath "C:\Windows\NTDS" -NoRebootOnCompletion:\$false -SiteName "Default-First-Site-Name" -SysvolPath "C:\Windows\SYSVOL" -Force:\$true}
   ```
1. 명령을 호출하려면 Enter 키를 누릅니다.
1. **Windows PowerShell 자격 증명 요청** 대화 상자의 **사용자 이름** 상자에 **CONTOSO\\Administrator** 를 입력하고 **암호** 상자에 **Pa55w.rd** 를 입력한 다음 **확인** 을 선택합니다.
1. 암호를 입력하라는 메시지가 표시되면 **SafeModeAdministratorPassword** 텍스트 상자에 **Pa55w.rd** 를 입력한 다음 Enter 키를 누릅니다.
1. 암호를 확인하라는 메시지가 표시되면 **SafeModeAdministratorPassword 확인** 텍스트 상자에 **Pa55w.rd** 를 입력한 다음 Enter 키를 누릅니다.
1. 명령이 실행되고 **상태 성공** 메시지가 반환될 때까지 기다립니다. **SEA-SVR1** 가상 머신이 다시 시작됩니다.
1. 파일을 저장하지 않고 메모장을 닫습니다.
1. **SEA-SVR1** 이 다시 시작되면 **SEA-ADM1** 에서 **서버 관리자** 로 전환하여 왼쪽에서 **AD DS** 노드를 선택합니다. **SEA-SVR1** 이 서버로 추가되었으며 경고 알림이 사라진 것을 알 수 있습니다.

   > **참고**: **새로 고침** 을 선택해야 할 수도 있습니다.

### <a name="task-3-manage-objects-in-ad-ds"></a>작업 3: AD DS에서 개체 관리

1. **SEA-ADM1** 의 콘솔 세션에 연결되어 있는지 확인합니다.
1. **Windows PowerShell(관리자)** 로 전환합니다.
1. Contoso AD DS 도메인에서 **Seattle** 이라는 OU(조직 구성 단위)를 만들려면 다음 명령을 입력한 다음 Enter 키를 누릅니다.

   ```powershell
   New-ADOrganizationalUnit -Name "Seattle" -Path "DC=contoso,DC=com" -ProtectedFromAccidentalDeletion $true -Server SEA-DC1.contoso.com
   ```
1. **Seattle** OU에서 **Ty Carlson** 의 사용자 계정을 만들려면 다음 명령을 입력한 다음 Enter 키를 누릅니다.

   ```powershell
   New-ADUser -Name Ty -DisplayName 'Ty Carlson' -GivenName Ty -Surname Carlson -Path 'OU=Seattle,DC=contoso,DC=com'
   ```
1. Ty의 사용자 계정에 대한 암호를 설정하려면 다음 명령을 입력한 다음 Enter 키를 누릅니다.

   ```powershell
   Set-ADAccountPassword Ty
   ```
1. 현재 암호를 묻는 메시지가 표시되면 Enter 키를 누릅니다.
1. 원하는 암호를 묻는 메시지가 표시되면 **Pa55w.rd** 를 입력한 다음 Enter 키를 누릅니다.
1. 암호를 반복하라는 메시지가 표시되면 **Pa55w.rd** 를 입력한 다음 Enter 키를 누릅니다.
1. 계정을 사용하도록 설정하려면 다음 명령을 입력한 다음 Enter 키를 누릅니다.

   ```powershell
   Enable-ADAccount Ty
   ```
1. **SeattleBranchUsers** 라는 도메인 전역 그룹을 만들려면 다음 명령을 입력한 다음 Enter 키를 누릅니다.

   ```powershell
   New-ADGroup SeattleBranchUsers -Path 'OU=Seattle,DC=contoso,DC=com' -GroupScope Global -GroupCategory Security
   ```
1. 새로 만든 그룹에 **Ty** 사용자 계정을 추가하려면 다음 명령을 입력한 다음 Enter 키를 누릅니다.

   ```powershell
   Add-ADGroupMember -Identity SeattleBranchUsers -Members Ty
   ```
1. 사용자가 그룹에 속하는지 확인하려면 다음 명령을 입력한 다음 Enter 키를 누릅니다.

   ```powershell
   Get-ADGroupMember -Identity SeattleBranchUsers
   ```
1. 로컬 관리자 그룹에 사용자를 추가하려면 다음 명령을 입력한 다음 Enter 키를 누릅니다.

   ```powershell
   Add-LocalGroupMember -Group 'Administrators' -Member 'CONTOSO\Ty'
   ```

   > **참고**: 이 작업은 **CONTOSO\\Ty** 사용자 계정으로 **SEA-ADM1** 에 로그인하도록 허용하기 위해 필요합니다.

**결과**: 이 연습이 끝나면 AD DS에서 새 도메인 컨트롤러 및 관리되는 개체를 성공적으로 만들게 됩니다.

## <a name="exercise-2-configuring-group-policy"></a>연습 2: 그룹 정책 구성

#### <a name="task-1-create-and-edit-a-gpo"></a>작업 1: GPO 만들기 및 편집

1. **SEA-ADM1** 의 서버 관리자에서 **도구** 를 선택한 다음 **그룹 정책 관리** 를 선택합니다.
1. 필요한 경우 **그룹 정책 관리** 창으로 전환합니다.
1. **그룹 정책 관리** 콘솔의 탐색 창에서 **Forest:Contoso.com**, **Domains** 및 **Contoso.com** 을 확장하고 **그룹 정책 개체** 컨테이너를 선택합니다.
1. 탐색 창에서 **그룹 정책 개체** 컨테이너를 마우스 오른쪽 단추로 클릭하거나 상황에 맞는 메뉴에 액세스하여 **새로 만들기** 를 선택합니다.
1. **이름** 텍스트 상자에 **CONTOSO Standards** 를 입력한 다음 **확인** 을 선택합니다.
1. 세부 정보 창에서 **CONTOSO Standards** GPO(그룹 정책 개체)를 마우스 오른쪽 단추로 클릭하거나 상황에 맞는 메뉴에 액세스하여 **편집** 을 선택합니다.
1. **그룹 정책 관리 편집기** 창의 탐색 창에서 **사용자 구성** 을 확장하고, **정책** 을 확장하고, **관리 템플릿** 을 확장한 다음 **시스템** 을 선택합니다.
1. **레지스트리 편집 도구에 대한 액세스 방지** 정책 설정을 두 번 클릭하거나 선택한 다음 Enter 키를 누릅니다.
1. **레지스트리 편집 도구에 대한 액세스 방지** 대화 상자에서 **사용** 을 선택하고 **확인** 을 선택합니다.
1. 탐색 창에서 **사용자** **구성** 을 확장하고, **정책** 을 확장하고, **관리 템플릿** 을 확장하고, **제어판** 을 확장한 다음, **개인 설정** 을 선택합니다.
1. 세부 정보 창에서 **화면 보호기 시간 제한** 정책 설정을 두 번 클릭하거나 선택한 다음 Enter 키를 누릅니다.
1. **화면 보호기 시간 제한** 대화 상자에서 **사용** 을 선택합니다. **초** 텍스트 상자에 **600** 을 입력한 다음 **확인** 을 선택합니다. 
1. **화면 보호기 암호로 보호** 정책 설정을 두 번 클릭하거나 선택한 다음 Enter 키를 누릅니다.
1. **화면 보호기 암호로 보호** 대화 상자에서 **사용** 을 선택한 다음 **확인** 을 선택합니다.
1. **그룹 정책 관리 편집기** 창을 닫습니다.

#### <a name="task-2-link-the-gpo"></a>작업 2: GPO 연결

1. **그룹 정책 관리** 창의 탐색 창에서 `Contoso.com` 도메인을 마우스 오른쪽 단추로 클릭하거나 상황에 맞는 메뉴에 액세스하여 **기존 GPO 연결** 을 선택합니다.
1. **GPO 선택** 대화 상자에서 **CONTOSO Standards** 를 선택한 다음 **확인** 을 선택합니다.

#### <a name="task-3-review-the-effects-of-the-gpos-settings"></a>작업 3: GPO 설정의 효과 검토

1. **SEA-ADM1** 의 작업 표시줄에 있는 검색 상자에 **제어판** 을 입력합니다. 
1. **가장 일치하는 항목** 목록에서 **제어판** 을 선택합니다.
1. **시스템 및 보안** 을 선택한 다음 **Windows 방화벽을 통해 앱 허용** 을 선택합니다.
1. **허용되는 앱 및 기능** 목록에서 **원격 이벤트 로그 관리** 항목을 찾아 **도메인** 열에서 확인란을 선택한 다음 **확인** 을 선택합니다. 
1. 로그아웃한 다음 암호 **Pa55w.rd** 를 사용하여 **CONTOSO\\Ty** 로 로그인합니다.
1. 작업 표시줄의 검색 상자에 **제어판** 을 입력합니다.
1. **가장 일치하는 항목** 목록에서 **제어판** 을 선택합니다.
1. 제어판의 검색 상자에 **화면 보호기** 를 입력한 다음 **화면 보호기 변경** 을 선택합니다. (이 옵션이 표시되는 데 몇 분 정도 걸릴 수 있습니다.)
1. **화면 보호기 설정** 대화 상자에서 **대기** 옵션이 흐리게 표시됩니다. 시간 제한은 변경할 수 없습니다. **다시 시작 시 로그온 화면 표시** 옵션이 선택되고 흐리게 표시되며 설정을 변경할 수 없습니다.

   > **참고**: **다시 시작 시 로그온 화면 표시** 옵션이 선택되고 흐리게 표시되지 않는 경우 명령 프롬프트를 열고 `gpupdate /force`을 실행한 다음 이전 단계를 반복합니다.

1. **시작** 을 마우스 오른쪽 단추로 클릭하거나 상황에 맞는 메뉴에 액세스하여 **실행** 을 선택합니다.
1. **실행** 대화 상자의 **열기** 텍스트 상자에 **regedit** 를 입력한 다음 **확인** 을 선택합니다. **관리자가 레지스트리 편집을 사용하지 않도록 설정했습니다.** 라는 오류 메시지가 표시됩니다.
1. **레지스트리 편집기** 대화 상자에서 **확인** 을 선택합니다.
1. 로그아웃한 다음 암호 **Pa55w.rd** 를 사용하여 **CONTOSO\\Administrator** 로 다시 로그인합니다.

#### <a name="task-4-create-and-link-the-required-gpos"></a>작업 4: 필요한 GPO 만들기 및 연결

1. **SEA-ADM1** 의 서버 관리자에서 **도구** 를 선택한 다음 **그룹 정책 관리** 를 선택합니다.
1. 필요한 경우 **그룹 정책 관리** 창으로 전환합니다.
1. **그룹 정책 관리** 콘솔의 탐색 창에서 **Forest:Contoso.com**, **Domains** 및 **Contoso.com** 을 확장하고 **Seattle** 을 선택합니다.
1. **Seattle** OU(조직 구성 단위)를 마우스 오른쪽 단추로 클릭하거나 상황에 맞는 메뉴에 액세스하여 **이 도메인에서 GPO 만들어 여기에 연결** 을 선택합니다.
1. **새 GPO** 대화 상자의 **이름** 텍스트 상자에 **Seattle Application Override** 를 입력한 다음 **확인** 을 선택합니다.
1. 세부 정보 창에서 **Seattle Application Override** GPO를 마우스 오른쪽 단추로 클릭하거나 상황에 맞는 메뉴에 액세스하여 **편집** 을 선택합니다.
1. **콘솔** 트리에서 **사용자 구성** 을 확장하고, **정책** 을 확장하고, **관리 템플릿** 을 확장하고, **제어판** 을 확장한 다음 **개인 설정** 을 선택합니다.
1. **화면 보호기 시간 제한** 정책 설정을 두 번 클릭하거나 선택한 다음 Enter 키를 누릅니다.
1. **사용 안 함** 을 선택한 다음 **확인** 을 선택합니다.
1. **그룹 정책 관리 편집기** 창을 닫습니다.

#### <a name="task-5-verify-the-order-of-precedence"></a>작업 5: 우선 순위 확인

1. **그룹 정책 관리 콘솔** 트리로 돌아가서 **Seattle** OU가 선택되어 있는지 확인합니다.
1. **그룹 정책 상속** 탭을 선택하고 내용을 검토합니다.

   > **참고**: Seattle Application Override GPO는 CONTOSO Standards GPO보다 우선 순위가 높습니다. Seattle Application Override GPO에서 방금 구성한 화면 보호기 시간 제한 정책 설정은 CONTOSO Standards GPO의 설정 다음에 적용됩니다. 따라서 새 설정이 CONTOSO Standards GPO 설정을 덮어씁니다. Seattle Application Override GPO 범위 내의 사용자에 대해 화면 보호기 시간 제한은 사용하지 않도록 설정됩니다.

#### <a name="task-6-configure-the-scope-of-a-gpo-with-security-filtering"></a>작업 6: 보안 필터링을 사용하여 GPO 범위 구성

1. **SEA-ADM1** 의 **그룹 정책 관리** 콘솔에 있는 탐색 창에서 필요한 경우 **Seattle** OU를 확장한 다음 **Seattle** OU에서 **Seattle Application Override** GPO를 선택합니다.
1. **그룹 정책 관리 콘솔** 대화 상자에서 다음 메시지를 검토합니다. **GPO(그룹 정책 개체)에 대한 링크를 선택했습니다. 링크 속성에 대한 변경 내용을 제외하고 여기에서 변경한 내용은 GPO에 전역적으로 적용되며 이 GPO가 연결된 다른 모든 위치에 영향을 미칩니다.**
1. **이 메시지를 다시 표시 안 함** 확인란을 선택한 다음 **확인** 을 선택합니다.
1. **보안 필터링** 섹션을 검토합니다. GPO가 기본적으로 인증된 사용자에게 적용됨을 알 수 있습니다.
1. **보안 필터링** 섹션에서 **인증된 사용자** 를 선택한 다음 **제거** 를 선택합니다.
1. **그룹 정책 관리** 대화 상자에서 **확인** 을 선택하고 **그룹 정책 관리** 경고를 검토한 다음 **확인** 을 다시 선택합니다.

   > **참고**: 그룹 정책에서는 각 컴퓨터 계정에 도메인 컨트롤러에서 GPO 데이터를 읽을 수 있는 권한이 있어야 사용자 GPO 설정을 성공적으로 적용할 수 있습니다. GPO의 보안 필터링 설정을 수정할 때는 이 점에 유의해야 합니다.

1. 세부 정보 창에서 **추가** 를 선택합니다.
1. **사용자, 컴퓨터 또는 그룹 선택** 대화 상자의 **선택할 개체 이름 입력(예시):** 텍스트 상자에 **SeattleBranchUsers** 를 입력한 다음 **확인** 을 선택합니다.
1. 세부 정보 창의 **보안 필터링** 에서 **추가** 를 선택합니다.
1. **사용자, 컴퓨터 또는 그룹 선택** 대화 상자에서 **개체 유형** 을 선택합니다.
1. **개체 유형** 대화 상자에서 **컴퓨터** 확인란을 선택한 다음 **확인** 을 선택합니다.
1. **사용자, 컴퓨터 또는 그룹 선택** 대화 상자의 **선택할 개체 이름 입력(예시):** 텍스트 상자에 **SEA-ADM1** 을 입력한 다음 **확인** 을 선택합니다.

#### <a name="task-7-verify-the-application-of-settings"></a>작업 7: 설정 적용 확인

1. 탐색 창의 **그룹 정책 관리** 에서 **그룹 정책 모델링** 을 선택합니다.
1. **그룹 정책 모델링** 을 마우스 오른쪽 단추로 클릭하거나 상황에 맞는 메뉴에 액세스하여 **그룹 정책 모델링 마법사** 를 선택합니다.
1. **그룹 정책 모델링 마법사** 에서 **다음** 을 선택합니다.
1. **도메인 컨트롤러 선택** 페이지에서 기본 설정을 적용하고 **다음** 을 선택합니다.
1. **사용자 및 컴퓨터 선택** 페이지의 **사용자 정보** 섹션에서 **사용자** 를 선택한 다음 **사용자** 텍스트 상자에 **CONTOSO\Ty** 를 입력하거나 **찾아보기** 명령 단추를 사용하여 **Ty** 사용자 계정을 찾습니다.
1. **사용자 및 컴퓨터 선택** 페이지의 **컴퓨터 정보** 섹션에서 **컴퓨터** 를 선택한 다음 **컴퓨터** 텍스트 상자에 **CONTOSO\SEA-ADM1** 을 입력합니다.
1. **사용자 및 컴퓨터 선택** 페이지에서 **다음** 을 선택합니다.
1. **고급 시뮬레이션 옵션** 페이지에서 기본 설정을 적용하고 **다음** 을 선택합니다.
1. **대체 Active Directory 경로** 페이지에서 사용자 및 컴퓨터 위치를 확인하고 **다음** 을 선택합니다.
1. **사용자 보안 그룹** 페이지에서 그룹 목록에 **CONTOSO\\SeattleBranchUsers** 가 포함되어 있는지 확인하고 **다음** 을 선택합니다.
1. **컴퓨터 보안 그룹** 페이지에서 **다음** 을 선택합니다.
1. **사용자에 대한 WMI 필터** 페이지에서 기본 설정을 적용하고 **다음** 을 선택합니다.
1. **컴퓨터에 대한 WMI 필터** 페이지에서 기본 설정을 적용하고 **다음** 을 선택합니다.
1. **선택 사항 요약** 페이지에서 **다음** 을 선택합니다.
1. 메시지가 표시되면 **마침** 을 선택합니다.
1. 세부 정보 창에서 **세부 정보** 탭을 선택한 다음 **모두 표시** 를 선택합니다.
1. 보고서에서 **사용자 세부 정보** 섹션이 나올 때까지 아래로 스크롤한 다음 **제어판/개인 설정** 섹션을 찾습니다. **화면 보호기 시간 제한** 설정이 사용 안 함으로 설정되고 최우선 GPO가 Seattle Application Override GPO로 설정됩니다.
1. **그룹 정책 관리** 콘솔을 닫습니다.

**결과**: 이 연습이 끝나면 GPO를 성공적으로 만들고 구성하게 됩니다.
