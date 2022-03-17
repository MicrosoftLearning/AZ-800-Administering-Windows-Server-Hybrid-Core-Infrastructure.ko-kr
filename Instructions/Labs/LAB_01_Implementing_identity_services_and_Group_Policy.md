---
lab:
  title: '랩: ID 서비스 및 그룹 정책 구현'
  module: 'Module 1: Identity services in Windows Server'
ms.openlocfilehash: 34c6259da5db4beca5e31998564c06102902ca48
ms.sourcegitcommit: bd43c7961e93ef200b92fb1d6f09d9ad153dd082
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2022
ms.locfileid: "137907051"
---
# <a name="lab-implementing-identity-services-and-group-policy"></a>랩: ID 서비스 및 그룹 정책 구현

## <a name="scenario"></a>시나리오

여러분은 Contoso Ltd에서 관리자로 일하고 있습니다. 이 회사는 여러 곳의 새로운 위치로 사업을 확장하고 있습니다. AD DS(Active Directory Domain Services) 관리 팀은 현재 Windows Server에서 비대화형 원격 도메인 컨트롤러 배포에 사용할 수 있는 방법을 평가하고 있습니다. 이 팀은 특정 AD DS 관리 작업을 자동화하는 방법도 찾고 있습니다. 또한 GPO(그룹 정책 개체)를 기반으로 구성 관리를 설정하려고 합니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음을 수행할 수 있습니다.

- Server Core에 새로운 도메인 컨트롤러 배포
- 그룹 정책 구성

## <a name="estimated-time-45-minutes"></a>예상 시간: 45분

## <a name="lab-setup"></a>랩 설정

가상 머신: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-ADM1**, **AZ-800T00A-SEA-SVR1** 을 실행해야 합니다. 다른 VM을 실행할 수도 있지만 이 랩에서는 필요하지 않습니다.

> **참고**: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-ADM1**, **AZ-800T00A-SEA-SVR1** 가상 머신은 **SEA-DC1**, **SEA-SVR1**, **SEA-ADM1** 의 설치를 호스트합니다. 

1. **SEA-ADM1** 을 선택합니다.
1. 다음 자격 증명을 사용하여 로그인합니다.

   - 사용자 이름: **Administrator**
   - 암호: **Pa55w.rd**
   - 도메인: **CONTOSO**

## <a name="exercise-1-deploying-a-new-domain-controller-on-server-core"></a>연습 1: Server Core에 새로운 도메인 컨트롤러 배포

### <a name="scenario"></a>시나리오

비즈니스 구조 조정의 일환으로 Contoso는 원격 위치에서 IT의 개입은 최소화하면서 원격 사이트에 새 도메인 컨트롤러를 배포하려고 합니다. DC 배포를 사용하여 새 도메인 컨트롤러를 배포해야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. 새 Windows Server Core 서버에 AD DS 배포
1. GUI 도구 및 Windows PowerShell을 통한 AD DS 개체 관리

#### <a name="task-1-deploy-ad-ds-on-a-new-windows-server-core-server"></a>작업 1: 새 Windows Server Core 서버에 AD DS 배포

1. **SEA-ADM1** 로 전환하고 **서버 관리자** 에서 Windows PowerShell을 엽니다.
1. Windows PowerShell에서 **Install-WindowsFeature** cmdlet을 사용하여 **SEA-SVR1** 에 AD DS 역할을 설치합니다.
1. **Get-WindowsFeature** cmdlet을 사용하여 설치를 확인합니다.
1. **Active Directory Domain Services**, **원격 서버 관리 도구** 및 **역할 관리 도구** 확인란을 선택해야 합니다. **AD DS** 및 **AD LDS 도구** 노드의 경우 **Windows PowerShell용 Active Directory 모듈** 만 설치되어 있고 Active Directory 관리 센터와 같은 그래픽 도구는 설치되어 있지 않습니다.

> **참고**: 서버를 중앙에서 관리하는 경우 일반적으로 각 서버에 GUI 도구가 필요하지는 않습니다. 이러한 도구를 설치하려는 경우 **RSAT-ADDS** 명령으로 **Add-WindowsFeature** cmdlet을 실행하여 AD DS 도구를 지정해야 합니다.

> **참고**: AD DS 역할이 설치되었는지 확인하기 전에 설치 프로세스가 완료된 후 기다려야 할 수 있습니다. **Get-WindowsFeature** 명령에서 예상되는 결과가 관찰되지 않는 경우 몇 분 후에 다시 시도할 수 있습니다.

#### <a name="task-2-prepare-the-ad-ds-installation-and-promote-a-remote-server"></a>작업 2: AD DS 설치 준비 및 원격 서버 승격

1. **SEA-ADM1** 에 있는 **서버 관리자** 의 **모든 서버** 노드에서 **SEA-SVR1** 을 서버 관리자로 추가합니다.
1. **SEA-ADM1** 에 있는 **서버 관리자** 에서 다음 설정을 사용하여 **SEA-SVR1** 을 AD DS 도메인 컨트롤러로 구성합니다.

   - 형식: 기존 도메인에 대한 추가 도메인 컨트롤러
   - 도메인: `contoso.com`
   - 자격 증명: 암호가 **Pa55w.rd** 인 **CONTOSO\\Administrator**
   - DSRM(디렉터리 서비스 복원 모드) 암호: **Pa55w.rd**
   - DNS 및 글로벌 카탈로그에 대한 선택 항목을 제거하지 마세요.

1. **검토 옵션** 페이지에서 **스크립트 보기** 를 선택합니다.
1. 메모장에서 생성된 Windows PowerShell 스크립트를 다음과 같이 편집합니다.

   - 숫자 기호(#)로 시작하는 주석 줄을 삭제합니다.
   - Import-Module 줄을 제거합니다.
   - 각 줄의 끝에 있는 악센트 기호(`)를 제거합니다.
   - 줄 바꿈을 제거합니다.

1. 이제 **Install-ADDSDomainController** 명령과 모든 매개 변수가 한 줄에 있습니다. 명령을 복사합니다.
1. **Active Directory Domain Services 구성 마법사** 로 전환한 다음 **취소** 를 선택합니다.
1. Windows PowerShell로 전환한 다음 명령 프롬프트에 다음 명령을 입력합니다.

   ```powershell
   Invoke-Command –ComputerName SEA-SVR1 { }
   ```
1. 중괄호({ }) 사이에 복사한 명령을 붙여넣고 결과 명령을 실행하여 설치를 시작합니다. 완료된 명령은 다음 형식이어야 합니다.

   ```powershell
   Invoke-Command –ComputerName SEA-SVR1 {Install-ADDSDomainController -NoGlobalCatalog:\$false -CreateDnsDelegation:\$false -Credential (Get-Credential) -CriticalReplicationOnly:\$false -DatabasePath "C:\Windows\NTDS" -DomainName "Contoso.com" -InstallDns:\$true -LogPath "C:\Windows\NTDS" -NoRebootOnCompletion:\$false -SiteName "Default-First-Site-Name" -SysvolPath "C:\Windows\SYSVOL" -Force:\$true}
   ```

1. 아래의 로그인 정보를 제공하십시오.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. **SafeModeAdministratorPassword** 를 **Pa55w.rd** 로 설정합니다.
1. **SEA-SVR1** 이 다시 시작되면 **SEA-ADM1** 에서 **서버 관리자** 로 전환하여 **AD DS** 노드를 선택합니다. **SEA-SVR1** 이 도메인 컨트롤러로 추가되었으며 경고 알림이 사라진 것을 알 수 있습니다. **새로 고침** 을 선택해야 할 수도 있습니다.

#### <a name="task-3-manage-objects-in-ad-ds"></a>작업 3: AD DS에서 개체 관리

1. **SEA-ADM1** 에서 **Windows PowerShell** 콘솔로 전환합니다.
1. **Seattle** 이라는 OU(조직 구성 단위)를 만들려면 **Windows PowerShell** 콘솔에서 다음 명령을 실행합니다.

   ```powershell
   New-ADOrganizationalUnit -Name "Seattle" -Path "DC=contoso,DC=com" -ProtectedFromAccidentalDeletion $true -Server SEA-DC1.contoso.com
   ```
1. **Seattle** OU에서 **Ty Carlson** 의 사용자 계정을 만들려면 다음 명령을 실행합니다.

   ```powershell
   New-ADUser -Name Ty -DisplayName 'Ty Carlson' -GivenName Ty -Surname Carlson -Path 'OU=Seattle,DC=contoso,DC=com'
   ```
1. 사용자의 암호를 **Pa55w.rd** 로 설정하려면 다음 명령을 실행합니다.

   ```powershell
   Set-ADAccountPassword Ty
   ```

> **참고**: 현재 암호는 비어 있습니다.

1. 사용자 계정을 활성화하려면 다음 명령을 실행합니다.

   ```powershell
   Enable-ADAccount Ty
   ```
1. **SeattleBranchUsers** 라는 도메인 전역 그룹을 만들려면 다음 명령을 실행합니다.

   ```powershell
   New-ADGroup SeattleBranchUsers -Path 'OU=Seattle,DC=contoso,DC=com' -GroupScope Global -GroupCategory Security
   ```
1. 새로 만든 그룹에 **Ty** 사용자 계정을 추가하려면 다음 명령을 실행합니다.

   ```powershell
   Add-ADGroupMember -Identity SeattleBranchUsers -Members Ty
   ```
1. 사용자가 그룹에 있는지 확인하려면 다음 명령을 실행합니다.

   ```powershell
   Get-ADGroupMember -Identity SeattleBranchUsers
   ```
1. 로컬 관리자 그룹에 사용자를 추가하려면 다음 명령을 실행합니다.

   ```powershell
   Add-LocalGroupMember -Group 'Administrators' -Member 'CONTOSO\Ty'
   ```

   > **참고**: 이 작업은 **CONTOSO\\Ty** 사용자 계정으로 **SEA-ADM1** 에 로그인하도록 허용하기 위해 필요합니다.

### <a name="results"></a>결과

이 연습이 끝나면 AD DS에서 새 도메인 컨트롤러 및 관리되는 개체를 성공적으로 만들게 됩니다.

## <a name="exercise-2-configuring-group-policy"></a>연습 2: 그룹 정책 구성

### <a name="scenario"></a>시나리오

그룹 정책 구현의 일부로 Office 앱의 사용자 지정 관리 템플릿을 가져오고 설정을 구성하려고 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. GPO 설정 만들기 및 편집
1. 클라이언트 컴퓨터에서 설정 적용 및 확인

### <a name="task-1-create-and-edit-a-gpo"></a>작업 1: GPO 만들기 및 편집

1. **SEA-ADM1** 의 **서버 관리자** 에서 **그룹 정책 관리** 콘솔을 엽니다.
1. **그룹 정책 개체** 컨테이너에 **Contoso Standards** 라는 GPO를 만듭니다.
1. 그룹 정책 관리 편집기에서 **Contoso Standards** GPO를 열고 **User Configuration\Policies\Administrative Templates\System** 으로 이동합니다.
1. **레지스트리 편집 도구 정책 설정에 대한 액세스 방지** 를 활성화합니다.
1. **User Configuration\Policies\Administrative Templates\Control Panel\Personalization** 폴더로 이동한 다음 **화면 보호기** 시간 제한 정책을 **600** 초로 구성합니다.
1. **화면 보호기 암호로 보호** 정책 설정을 활성화하고 **그룹 정책 관리 편집기** 창을 닫습니다.

### <a name="task-2-link-the-gpo"></a>작업 2: GPO 연결

  - **Contoso Standards** GPO를 `contoso.com` 도메인에 연결합니다.

### <a name="task-3-review-the-effects-of-the-gpos-settings"></a>작업 3: GPO 설정의 효과 검토

1. **SEA-ADM1** 에서 **제어판** 을 엽니다.
1. **Windows Defender 방화벽** 인터페이스를 사용하여 **원격 이벤트 로그 관리** 도메인 트래픽을 활성화합니다. 
1. 로그아웃한 다음 암호 **Pa55w.rd** 를 사용하여 **CONTOSO\\Ty** 로 로그인합니다.
1. 화면 보호기 대기 시간을 변경하고 설정을 다시 시작합니다. 그룹 정책이 이러한 작업을 차단하는지 확인합니다.
1. 레지스트리 편집기를 실행하려고 시도해 봅니다. 그룹 정책이 이러한 작업을 차단하는지 확인합니다. 
1. 로그아웃한 다음 암호 **Pa55w.rd** 를 사용하여 **CONTOSO\\Administrator** 로 다시 로그인합니다.

#### <a name="task-4-create-and-link-the-required-gpos"></a>작업 4: 필요한 GPO 만들기 및 연결

1. **SEA-ADM1** 의 **그룹 정책 관리** 콘솔에서 **Seattle** OU에 연결된 **Application Override** 라는 이름의 새 GPO를 만듭니다.
1. **화면 보호기 시간 제한** 정책 설정이 비활성화되도록 구성한 다음 **그룹 정책 관리 편집기** 창을 닫습니다.

### <a name="task-5-verify-the-order-of-precedence"></a>작업 5: 우선 순위 확인

1. **SEA-ADM1** 의 **서버 관리자** 에서 **그룹 정책 관리** 콘솔을 엽니다.
1. **그룹 정책 관리 콘솔** 트리에서 **Seattle** OU를 선택합니다.
1. **그룹 정책 상속** 탭을 선택하고 내용을 검토합니다.

   > **참고**: Seattle Application Override GPO는 CONTOSO Standards GPO보다 우선 순위가 높습니다. Seattle Application Override GPO에서 방금 구성한 화면 보호기 시간 제한 정책 설정은 CONTOSO Standards GPO의 설정 다음에 적용됩니다. 따라서 새 설정이 CONTOSO Standards GPO 설정을 덮어씁니다. Seattle Application Override GPO 범위 내의 사용자에 대해 화면 보호기 시간 제한은 사용하지 않도록 설정됩니다.

### <a name="task-6-configure-the-scope-of-a-gpo-with-security-filtering"></a>작업 6: 보안 필터링을 사용하여 GPO 범위 구성

1. **SEA-ADM1** 의 **그룹 정책 관리** 콘솔에서 **Seattle Application Override** GPO를 선택합니다. **보안 필터링** 섹션에서 GPO가 기본적으로 인증된 사용자에게 적용됨을 알 수 있습니다.
1. **보안 필터링** 섹션에서 먼저 **인증된 사용자** 를 제거한 다음 **SeattleBranchUsers** 그룹 및 **SEA-ADM1** 컴퓨터 계정을 추가합니다.

### <a name="task-7-verify-the-application-of-settings"></a>작업 7: 설정 적용 확인

1. 그룹 정책 관리의 탐색 창에서 **그룹 정책 모델링** 을 선택합니다.
1. **그룹 정책 모델링 마법사** 를 실행합니다.
1. 대상 사용자와 컴퓨터를 각각 **CONTOSO\\Ty** 사용자 계정과 **SEA-ADM1** 컴퓨터로 설정합니다.
1. 마법사의 나머지 페이지를 단계별로 실행하고, 기본 설정을 수정하지 않은 상태로 검토하며, 마법사를 완료하여 결과가 포함된 보고서를 생성합니다.
1. 보고서를 만든 후 세부 정보 창에서 **세부 정보** 탭을 선택하고 **모두 표시** 를 선택합니다.
1. 보고서에서 **사용자 세부 정보** 섹션이 나올 때까지 아래로 스크롤한 다음 **제어판/개인 설정** 섹션을 찾습니다. Seattle Application Override GPO에서 **화면 보호기 시간 제한** 설정을 가져왔는지 확인해야 합니다.
1. **그룹 정책 관리** 콘솔을 닫습니다.

### <a name="results"></a>결과

이 연습이 끝나면 GPO를 성공적으로 만들고 구성하게 됩니다.
