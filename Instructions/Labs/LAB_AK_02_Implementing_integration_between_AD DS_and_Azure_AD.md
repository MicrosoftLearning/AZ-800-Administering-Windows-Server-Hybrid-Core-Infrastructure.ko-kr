---
lab:
  title: '랩: AD DS와 Azure AD 간의 통합 구현'
  type: Answer Key
  module: 'Module 2: Implementing Identity in Hybrid Scenarios'
ms.openlocfilehash: 8cbcc563a86ca5c1c997a69884e1b132cbf2f86e
ms.sourcegitcommit: bd43c7961e93ef200b92fb1d6f09d9ad153dd082
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2022
ms.locfileid: "137906994"
---
# <a name="lab-implementing-integration-between-ad-ds-and-azure-ad"></a>랩: AD DS와 Azure AD 간의 통합 구현

## <a name="exercise-1-preparing-azure-ad-for-ad-ds-integration"></a>연습 1: AD DS 통합을 위한 Azure AD 준비

#### <a name="task-1-create-a-custom-domain-in-azure"></a>작업 1: Azure에서 사용자 지정 도메인 만들기

1. **SEA-ADM1** 에 연결하고, 필요하면 **Pa55w.rd** 암호를 사용해 **Contoso\\Administrator** 로 로그인합니다.
1. **SEA-ADM1** 에서 Microsoft Edge를 시작하고 Azure Portal로 이동한 다음 Azure 자격 증명으로 인증합니다.
1. Azure Portal에서 **Azure Active Directory** 로 이동합니다.
1. **Azure Active Directory** 페이지에서 **사용자 지정 도메인 이름** 을 선택합니다.
1. **사용자 지정 도메인 이름** 페이지에서 **사용자 지정 도메인 추가** 를 선택합니다.
1. **사용자 지정 도메인 이름** 창의 **사용자 지정 도메인 이름** 텍스트 상자에 **contoso.com** 을 입력한 다음 **도메인 추가** 를 선택합니다.
1. `contoso.com` 사용자 지정 도메인 이름 페이지에서 도메인 확인에 사용할 DNS(Domain Name System) 레코드 유형을 검토합니다.
1. 도메인 이름을 확인하지 않고 창을 닫습니다.

   > **참고**: 일반적으로는 DNS 레코드를 사용해 도메인을 확인하지만 이 랩에서는 확인된 도메인을 사용할 필요가 없습니다.

#### <a name="task-2-create-a-user-with-the-global-administrator-role"></a>작업 2: 전역 관리자 역할을 가진 사용자 만들기

1. **SEA-ADM1** 의 Azure Portal의 **Azure Active Directory** 페이지에서 **사용자** 를 선택합니다.
1. **모든 사용자** 페이지에서 **새 사용자** 를 선택합니다.
1. **새 사용자** 페이지에서 **ID** 밑에 있는 **사용자 이름** 및 **이름** 텍스트 상자에 **admin** 을 입력합니다.

   > **참고**: **사용자 이름** 의 도메인 이름 드롭다운 메뉴에 `onmicrosoft.com`로 끝나는 기본 도메인 이름이 나열되는지 확인합니다.

1. **암호** 아래에서 **암호 표시** 확인란을 선택합니다. 이 랩의 뒷부분에서 사용할 암호를 기록합니다.
1. **그룹 및 역할** 에서 **역할** 옆에 있는 **사용자** 를 선택합니다.
1. **디렉터리 역할** 페이지의 역할 목록에서 **전역 관리자** 를 선택한 다음, **선택** 을 선택합니다.
1. **새 사용자** 페이지로 돌아가 **사용 위치** 드롭다운 목록에서 **미국** 을 선택합니다.
1. **새 사용자** 페이지에서 **만들기** 를 선택합니다.

#### <a name="task-3-change-the-password-for-the-user-with-the-global-administrator-role"></a>작업 3: 전역 관리자 역할을 가진 사용자의 암호 변경

1. Azure Portal에서 사용자 이름을 선택한 다음 **로그아웃** 을 선택합니다.
1. **계정 선택** 페이지에서 **다른 계정 사용** 을 선택합니다.
1. **로그인** 페이지에서 이전에 만든 사용자 계정의 정규화된 사용자 이름을 입력한 다음, **다음** 을 선택합니다.
1. 이전 단계에서 기록한 암호를 현재 암호로 사용합니다.
1. 복잡한 암호를 두 번 입력한 다음 **로그인** 을 선택합니다.

   > **참고**: 사용한 복잡한 암호를 기록하세요. 이 랩의 뒷부분에서 사용하게 될 것입니다.

## <a name="exercise-2-preparing-on-premises-ad-ds-for-azure-ad-integration"></a>연습 2: Azure AD 통합을 위해 온-프레미스 AD DS 준비

#### <a name="task-1-install-idfix"></a>작업 1: IdFix 설치

1. **SEA-ADM1** 에서 Microsoft Edge 연 다음 **https://github.com/microsoft/idfix** 로 이동합니다.
1. **Github** 페이지의 **ClickOnce 시작** 에서 **시작** 을 선택합니다.
1. 상태 표시줄에서 **파일 열기** 를 선택합니다.
1. **애플리케이션 설치 - 보안 경고** 대화 상자에서 **설치** 를 선택합니다.
1. **IdFix 개인정보처리방침** 대화 상자에서 고지 사항을 검토한 다음 **확인** 을 선택합니다.

#### <a name="task-2-run-idfix"></a>작업 2: IdFix 실행

1. **IdFix** 창에서 **쿼리** 를 선택합니다.
1. **스키마 경고** 대화 상자가 표시되면 **예** 를 선택합니다.
1. Active Directory의 개체 목록을 검토하고 **ERROR** 및 **ATTRIBUTE** 열을 확인합니다. 이 시나리오는 **ContosoAdmin** 의 **displayName** 값이 비어 있으며, 도구에서 권장하는 새 값이 **UPDATE** 열에 나타납니다.
1. **IdFix** 창의 **작업** 드롭다운 메뉴에서 **편집** 을 선택한 다음 **적용** 을 선택하여 권장 변경 내용을 자동으로 구현합니다.
1. **보류 적용** 대화 상자에서 **예** 를 선택합니다.
1. IdFix 도구를 닫습니다.

## <a name="exercise-3-downloading-installing-and-configuring-azure-ad-connect"></a>연습 3: Azure AD Connect 다운로드, 설치 및 구성

#### <a name="task-1-install-and-configure-azure-ad-connect"></a>작업 1: Azure AD Connect 설치 및 구성

1. **SEA-ADM1** 의 Azure Portal을 표시하는 Microsoft Edge 창에서 **Azure Active Directory** 로 이동합니다.
1. **Azure Active Directory** 탭에서 **Azure AD Connect** 를 선택합니다.
1. **Azure AD Connect** 페이지에서 **Azure AD Connect 다운로드** 를 선택합니다.
1. **Microsoft Azure Active Directory Connect** 페이지에서 **다운로드** 를 선택합니다.
1. 상태 표시줄에서 **파일 열기** 를 선택합니다.
1. **Microsoft Azure Active Directory Connect** 페이지에서 **사용 조건 및 개인정보처리방침에 동의** 확인란을 선택한 다음 **계속** 을 선택합니다.
1. **Express 설정** 페이지에서 **기본 설정 사용** 을 선택합니다.
1. **Azure AD에 연결** 페이지에서, 연습 1에서 만든 Azure AD 전역 관리자 사용자 계정의 사용자 이름과 암호를 입력한 다음, **다음** 을 선택합니다.
1. **AD DS에 연결** 페이지에서 다음 자격 증명을 입력한 다음, **다음** 을 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. **Azure AD 로그인 구성** 페이지에서 추가한 새 도메인은 Active Directory UPN 접미사 목록에 있지만 그 상태는 **확인되지 않음** 으로 표시됩니다.

   > **참고**: 제공된 도메인 이름은 확인된 도메인일 필요가 없습니다. 일반적으로는 Azure AD Connect 설치 전에 도메인을 확인하겠지만 이 랩에서는 이 확인 단계가 필요 없습니다.

1. **모든 UPN 접미사를 확인된 도메인에 일치시키지 않고 계속** 을 선택한 다음, **다음** 을 선택합니다.
1. **구성 준비** 페이지에서 작업 목록을 검토한 다음 **설치** 를 선택합니다.
1. **구성 완료** 페이지에서 **끝내기** 를 선택합니다.

## <a name="exercise-4-verifying-integration-between-ad-ds-and-azure-ad"></a>연습 4: AD DS와 Azure AD 간의 통합 확인

#### <a name="task-1-verify-synchronization-in-the-azure-portal"></a>작업 1: Azure Portal에서 동기화 확인

1. **SEA-ADM1** 에서 Azure Portal이 표시된 Microsoft Edge 창으로 전환합니다. 
1. **Azure AD Connect** 페이지를 새로 고치고 **Active Directory에서 프로비저닝** 아래의 정보를 검토합니다.
1. **Azure Active Directory** 페이지에서 **사용자** 를 선택합니다.
1. 사용자 목록에는 Active Directory에서 동기화된 사용자가 포함됩니다.

   > **참고**: 디렉터리 동기화가 시작된 후 Active Directory 개체가 Azure AD 포털에 표시되는 데 15분이 걸릴 수 있습니다.

1. Microsoft Edge에서 **Azure Active Directory** 페이지로 돌아갑니다.
1. **Azure Active Directory** 페이지에서 **그룹** 을 선택합니다.
1. Active Directory에서 동기화된 그룹 목록을 확인합니다. 

#### <a name="task-2-verify-synchronization-in-the-synchronization-service-manager"></a>작업 2: Synchronization Service Manager에서 동기화 확인

1. **SEA-ADM1** 의 **시작** 메뉴에서 **Azure AD Connect** 를 확장한 다음 **동기화 서비스** 를 선택합니다.
1. **Synchronization Service Manager** 창의 **작업** 탭 아래에서 Active Directory 개체를 동기화하기 위해 수행된 작업을 확인합니다.
1. **커넥터** 탭을 선택하고 두 커넥터를 메모합니다.

   > **참고**: 한 커넥터는 AD DS용이고 다른 커넥터는 Azure AD 테넌트용입니다. 

1. **Synchronization Service Manager** 창을 닫습니다.

#### <a name="task-3-update-a-user-account-in-active-directory"></a>작업 3: Active Directory에서 사용자 계정 업데이트

1. **SEA-ADM1** 의 서버 관리자에 있는 **도구** 메뉴에서 **Active Directory 사용자 및 컴퓨터** 를 선택합니다.
1. **Active Directory 사용자 및 컴퓨터** 에서 **영업** OU(조직 구성 단위)를 확장한 다음 **Ben Miller** 의 속성을 엽니다.
1. 사용자의 속성에서 **조직** 탭을 선택합니다.
1. **직함** 텍스트 상자에 **관리자** 를 입력한 다음 **확인** 을 선택합니다.

#### <a name="task-4-create-a-user-account-in-active-directory"></a>작업 4: Active Directory에서 사용자 계정 만들기

1. **Active Directory 사용자 및 컴퓨터** 에서 **영업** OU의 상황에 맞는 메뉴를 마우스 오른쪽 단추로 클릭하거나 메뉴에 액세스하고 **새로 만들기** 를 선택한 다음 **사용자** 를 선택합니다.
1. **새 개체 - 사용자** 창에서 각 필드에 대해 다음 사용자 세부 정보를 입력한 다음, **다음** 을 선택합니다.

   - 이름: **Jordan**
   - 성: **Mitchell**
   - 사용자 로그온 이름: **Jordan**

1. **암호** 및 **암호 확인** 필드에 **Pa55w.rd** 를 입력한 다음, **다음** 을 선택합니다.
1. **마침** 을 선택합니다.

#### <a name="task-5-sync-changes-to-azure-ad"></a>작업 5: Azure AD로 변경 내용 동기화

1. **SEA-ADM1** 의 **시작** 메뉴에서 **Windows PowerShell** 을 선택합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter를 눌러 동기화를 트리거합니다.

   ```powershell
   Start-ADSyncSyncCycle
   ```

   > **참고**: 동기화 사이클이 시작된 후 Active Directory 개체가 Azure AD 포털에 표시되는 데 15분이 걸릴 수 있습니다.

#### <a name="task-6-verify-changes-in-azure-ad"></a>작업 6: Azure AD에서 변경 내용 확인

1. **SEA-ADM1** 에서 Azure Portal을 표시하는 Microsoft Edge 창으로 전환하고 **Azure Active Directory** 페이지로 돌아갑니다.
1. **Azure Active Directory** 페이지에서 **사용자** 를 선택합니다.
1. **모든 사용자** 페이지에서 사용자 **Ben** 을 검색합니다.
1. 사용자 **Ben Miller** 의 속성 페이지를 열고 Active Directory에서 **직함** 특성이 동기화되었는지 확인합니다.
1. Microsoft Edge에서 **모든 사용자** 페이지로 돌아갑니다.
1. **모든 사용자** 페이지에서 사용자 **Jordan** 을 검색합니다.
1. 사용자 **Jordan Mitchell** 의 속성 페이지를 열고 Active Directory에서 동기화된 사용자 계정의 특성을 검토합니다.

## <a name="exercise-5-implementing-azure-ad-integration-features-in-ad-ds"></a>연습 5: AD DS에서 Azure AD 통합 기능 구현

#### <a name="task-1-enable-self-service-password-reset-in-azure"></a>작업 1: Azure에서 셀프 서비스 암호 재설정을 사용하도록 설정

1. **SEA-ADM1** 에서, Azure Portal을 표시하는 Microsoft Edge 창에서 **Azure Active Directory** 로 이동합니다.
1. **Azure Active Directory** 페이지에서 **라이선스** 를 선택합니다.
1. **라이선스** 페이지에서 **모든 제품** 을 선택합니다.
1. **모든 제품** 페이지에서 **+ 체험/구매** 를 선택합니다.
1. **활성화** 페이지의 **AZURE AD PREMIUM P2** 에서 **무료 평가판** 을 선택한 다음 **활성화** 를 선택합니다.
1. **모든 제품** 페이지로 이동하여 **Azure Active Directory Premium P2** 를 선택합니다.
1. **Azure Active Directory Premium P2\| 라이선스 사용자** 페이지에서 **+ 할당** 을 선택합니다.
1. **라이선스 할당** 페이지에서 **+ 사용자 및 그룹 추가** 를 선택합니다.
1. **사용자 및 그룹 추가** 페이지에서 **관리자** 를 검색하고 결과 목록에서 **관리자** 계정을 선택한 다음 **선택** 을 선택합니다.
1. **라이선스 할당** 페이지로 돌아가서 **검토 + 할당** 을 선택한 다음 **할당** 을 선택합니다.

   > **참고**: 이 랩의 뒷부분에서 Azure AD 암호 보호를 구현하려면 이 작업이 필요합니다.

1. **Azure Active Directory** 페이지로 돌아갑니다.
1. **Azure Active Directory** 페이지에서 **암호 재설정** 을 선택합니다.
1. **암호 재설정** 페이지에서 구성을 적용할 사용자의 범위를 선택할 수 있습니다.

   > **참고**: 암호 재설정 기능은 이 랩의 뒷부분에서 필요한 구성 단계를 중단하므로 사용하도록 설정하지 마세요.

#### <a name="task-2-enable-password-writeback-in-azure-ad-connect"></a>작업 2: Azure AD Connect에서 암호 쓰기 저장을 사용하도록 설정

1. **SEA-ADM1** 의 **시작** 메뉴에서 **Azure AD Connect** 를 확장한 다음 **Azure AD Connect** 를 선택합니다.
1. **Microsoft Azure Active Directory Connect** 창에서 **구성** 을 선택합니다.
1. **추가 작업** 페이지에서 **동기화 옵션 사용자 지정** 을 선택하고 **다음** 을 선택합니다.
1. **Azure AD에 연결** 페이지에서, 연습 1에서 만든 Azure AD 전역 관리자 사용자 계정의 사용자 이름과 암호를 입력한 다음, **다음** 을 선택합니다.
1. **디렉터리 연결** 페이지에서 **다음** 을 선택합니다.
1. **도메인 및 OU 필터링** 페이지에서 **다음** 을 선택합니다.
1. **선택적 기능** 페이지에서 **암호 쓰기 저장** 을 선택하고 **다음** 을 선택합니다.

   > **참고**: Active Directory 사용자의 셀프 서비스 암호 재설정에는 암호 쓰기 저장이 필요합니다. 이렇게 하면 Azure AD의 사용자가 변경한 암호를 Active Directory와 동기화할 수 있습니다.

1. **구성 준비 완료** 페이지에서 수행할 작업 목록을 검토한 다음 **구성** 을 선택합니다.
1. **구성 완료** 페이지에서 **끝내기** 를 선택합니다.

#### <a name="task-3-enable-pass-through-authentication-in-azure-ad-connect"></a>작업 3: Azure AD Connect에서 통과 인증을 사용하도록 설정

1. **SEA-ADM1** 의 **시작** 메뉴에서 **Azure AD Connect** 를 확장한 다음 **Azure AD Connect** 를 선택합니다.
1. **Microsoft Azure Active Directory Connect** 창에서 **구성** 을 선택합니다.
1. **추가 작업** 페이지에서 **사용자 로그인 변경** 을 선택한 다음, **다음** 을 선택합니다.
1. **Azure AD에 연결** 페이지에서, 연습 1에서 만든 Azure AD 전역 관리자 사용자 계정의 사용자 이름과 암호를 입력한 다음, **다음** 을 선택합니다.
1. **사용자 로그인** 페이지에서 **통과 인증** 을 선택합니다.
1. **Single Sign-On 사용** 확인란이 선택되어 있는지 확인한 다음, **다음** 을 선택합니다.
1. **Single Sign-On 사용** 페이지에서 **자격 증명 입력** 을 선택합니다.
1. **포리스트 자격 증명** 대화 상자에서 다음 자격 증명을 입력한 다음 **확인** 을 선택합니다.

   - 사용자 이름: **Administrator**
   - 암호: **Pa55w.rd**

1. **Single Sign-On 사용** 페이지에서 **자격 증명 입력** 옆에 녹색 확인 표시가 있는지 확인한 다음, **다음** 을 선택합니다.
1. **구성 준비 완료** 페이지에서 수행할 작업 목록을 검토한 다음 **구성** 을 선택합니다.
1. **구성 완료** 페이지에서 **끝내기** 를 선택합니다.

#### <a name="task-4-verify-pass-through-authentication-in-azure"></a>작업 4: Azure에서 통과 인증 확인

1. **SEA-ADM1** 에서 Azure Portal을 표시하는 Microsoft Edge 창으로 전환하고 **Azure Active Directory** 페이지로 돌아갑니다.
1. Azure Portal의 **Azure Active Directory** 페이지에서 **Azure AD Connect** 를 선택합니다.
1. **Azure AD Connect** 페이지에서 **사용자 로그인** 아래의 정보를 검토합니다.
1. **사용자 로그인** 에서 **Seamless Single Sign-On** 을 선택합니다.
1. **Seamless Single Sign-On** 페이지에서 온-프레미스 도메인 이름을 확인합니다.
1. Microsoft Edge에서 **Azure AD Connect** 페이지로 돌아갑니다.
1. **Azure AD Connect** 페이지의 **사용자 로그인** 아래에서 **통과 인증** 을 선택합니다.
1. **통과 인증** 페이지에서 **인증 에이전트** 아래의 **SEA-ADM1** 서버 이름을 확인합니다.

   > **참고**: 사용자 환경의 여러 서버에 Azure AD 인증 에이전트를 설치하려면 Azure Portal의 **통과 인증** 페이지에서 이진 파일을 다운로드하면 됩니다. 

#### <a name="task-5-install-and-register-the-azure-ad-password-protection-proxy-service-and-dc-agent"></a>작업 5: Azure AD 암호 보호 프록시 서비스 및 DC 에이전트 설치 및 등록

1. **SEA-ADM1** 에서 Microsoft Edge 시작하고 Microsoft 다운로드 웹 사이트로 이동하여 설치 관리자를 다운로드할 수 있는 **Windows Server Active Directory용 Azure AD 암호 보호** 페이지로 이동한 다음 **다운로드** 를 선택합니다.
1. **Windows Server Active Directory용 Azure AD 암호 보호** 페이지에서 **AzureADPasswordProtectionProxySetup.msi** 및 **AzureADPasswordProtectionDCAgentSetup.msi** 파일을 선택한 다음, **다음** 을 선택합니다.
1. **다운로드** 를 선택합니다.
1. **여러 파일 다운로드** 대화 상자에서 **허용** 을 선택합니다.

   > **참고**: 도메인 컨트롤러가 아닌 서버에 프록시 서비스를 설치하는 것이 좋습니다. 또한 프록시 서비스는 Azure AD Connect 에이전트와 동일한 서버에 설치하면 안 됩니다. **SEA-SVR1** 에 프록시 서비스를 설치하고 **SEA-DC1** 의 암호 보호 DC 에이전트를 설치합니다.

1. **SEA-ADM1** 에서 **Windows PowerShell** 콘솔 창으로 전환합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter를 눌러 인터넷에서 파일이 다운로드되었음을 나타내는 Zone.Identifier 대체 데이터 스트림을 제거합니다.

   ```powershell
   Get-ChildItem -Path "$env:USERPROFILE\Downloads" -File | Unblock-File
   ```
1. 다음 명령을 실행하여 **SEA-SVR1** 에서 **C:\Temp** 디렉터리를 만들고 **AzureADPasswordProtectionProxySetup.msi** 설치 관리자를 해당 디렉터리에 복사한 다음, 설치를 호출합니다.

   ```powershell
   New-Item -Type Directory -Path '\\SEA-SVR1.contoso.com\C$\Temp' -Force
   Copy-Item -Path "$env:USERPROFILE\Downloads\AzureADPasswordProtectionProxySetup.msi" -Destination '\\SEA-SVR1.contoso.com\C$\Temp\'
   Invoke-Command -ComputerName SEA-SVR1.contoso.com -ScriptBlock { Start-Process -FilePath C:\Temp\AzureADPasswordProtectionProxySetup.msi -ArgumentList '/quiet /log C:\Temp\AzureADPPProxyInstall.log' -Wait }
   ```
1. 다음 명령을 실행하여 **SEA-DC1** 에서 **C:\Temp** 디렉터리를 만들고 **AzureADPasswordProtectionDCAgentSetup.msi** 설치 관리자를 해당 디렉터리에 복사하고, 설치를 호출한 다음 설치 완료 후 도메인 컨트롤러를 다시 시작합니다.

   ```powershell
   New-Item -Type Directory -Path '\\SEA-DC1.contoso.com\C$\Temp' -Force
   Copy-Item -Path "$env:USERPROFILE\Downloads\AzureADPasswordProtectionDCAgentSetup.msi" -Destination '\\SEA-DC1.contoso.com\C$\Temp\'
   Invoke-Command -ComputerName SEA-DC1.contoso.com -ScriptBlock { Start-Process msiexec.exe -ArgumentList '/i C:\Temp\AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart /log C:\Temp\AzureADPPInstall.log' -Wait }
   Restart-Computer -ComputerName SEA-DC1.contoso.com -Force
   ```
1. 다음 명령을 실행하여 설치로 인해 Azure AD 암호 보호를 구현하는 데 필요한 서비스가 생성되었는지 확인합니다.

   ```powershell
   Get-Service -Computer SEA-SVR1 -Name AzureADPasswordProtectionProxy | fl
   Get-Service -Computer SEA-DC1 -Name AzureADPasswordProtectionDCAgent | fl
   ```

   > **참고**: 각 서비스 상태가 **실행** 인지 확인합니다.

1. **Windows PowerShell** 콘솔에서 다음 명령을 입력하고 Enter를 눌러 **SEA-SVR1** 에 대한 PowerShell 원격 세션을 설정합니다.

   ```powershell
   Enter-PSSession -ComputerName SEA-SVR1
   ```

1. **[SEA-SVR1]** 프롬프트에서 다음 명령을 입력하고 Enter를 눌러 Active Directory에 프록시 서비스를 등록합니다(`<Azure_AD_Global_Admin>` 자리 표시자를 연습 1에서 만든 AD 전역 관리자 계정의 정규화된 사용자 계정 이름으로 바꿉니다).

   ```powershell
   Register-AzureADPasswordProtectionProxy -AccountUpn <Azure_AD_Global_Admin> -AuthenticateUsingDeviceCode
   ```

1. 지시에 따라 다른 Microsoft Edge 창을 열어 **https://microsoft.com/devicelogin** 로 이동하고 메시지가 표시되면 PowerShell 원격 세션에 표시된 메시지에 포함된 코드를 입력합니다. 
1. 메시지가 표시되면 연습 1에서 만든 Azure AD 전역 관리자 사용자 계정을 사용하여 인증한 다음 **계속** 을 선택합니다.
1. PowerShell 원격 세션으로 다시 전환해 다음 명령을 입력하고, Enter를 눌러 **SEA-SVR1** 의 PowerShell 원격 세션을 종료합니다.

   ```powershell
   Exit-PSsession
   ```

1. **Windows PowerShell** 콘솔에서 다음 명령을 입력하고 Enter를 눌러 **SEA-DC1** 에 대한 PowerShell 원격 세션을 설정합니다.

   ```powershell
   Enter-PSSession -ComputerName SEA-DC1
   ```

1. **[SEA-DC1]** 프롬프트에서 다음 명령을 입력하고 Enter를 눌러 Active Directory에 프록시 서비스를 등록합니다(`<Azure_AD_Global_Admin>` 자리 표시자를 연습 1에서 만든 Azure AD 전역 관리자 사용자 계정의 정규화된 사용자 계정 이름으로 바꿉니다).

   ```powershell
   Register-AzureADPasswordProtectionForest -AccountUpn <Azure_AD_Global_Admin> -AuthenticateUsingDeviceCode
   ```

1. 지시에 따라 다른 Microsoft Edge 창을 열어 **https://microsoft.com/devicelogin** 로 이동하고 메시지가 표시되면 PowerShell 원격 세션에 표시된 메시지에 포함된 코드를 입력합니다. 
1. 메시지가 표시되면 연습 1에서 만든 Azure AD 전역 관리자 사용자 계정을 사용하여 인증한 다음 **계속** 을 선택합니다.
1. PowerShell 원격 세션으로 다시 전환해 다음 명령을 입력하고, Enter를 눌러 **SEA-DC1** 의 PowerShell 원격 세션을 종료합니다.

   ```powershell
   Exit-PSsession
   ```

#### <a name="task-6-enable-password-protection-in-azure"></a>작업 6: Azure에서 암호 보호를 사용하도록 설정

1. **SEA-ADM1** 에서 Azure Portal을 표시하는 Microsoft Edge 창으로 전환하고, **Azure Active Directory** 페이지로 돌아간 다음 **Azure Active Directory** 페이지에서 **보안** 을 선택합니다.
1. **보안** 페이지에서 **인증 방법** 을 선택합니다.
1. **인증 방법** 페이지에서 **암호 보호** 를 선택합니다.
1. **암호 보호** 페이지에서 **사용자 지정 목록 적용** 슬라이더를 **예** 로 변경합니다.
1. **사용자 지정 금지 암호 목록** 텍스트 상자에 다음 단어(줄당 하나씩)를 입력합니다.
 
   - **Contoso**
   - **런던**

   > **참고**: 금지된 암호 목록은 조직과 관련된 단어여야 합니다.

1. **Windows Server Active Directory에서 암호 보호 사용** 이 **예** 로 설정되어 있는지 확인합니다.
1. **모드** 의 슬라이더가 **감사** 로 설정되어 있는지 확인한 다음 **저장** 을 선택합니다.

## <a name="exercise-6-cleaning-up"></a>연습 6: 정리

#### <a name="task-1-uninstall-azure-ad-connect"></a>작업 1: Azure AD Connect 제거

1. **SEA-ADM1** 의 **시작** 메뉴에서 **제어판** 을 선택합니다.
1. **제어판** 창의 **프로그램** 에서 **프로그램 제거** 를 선택합니다.
1. **프로그램 제거 또는 변경** 창에서 **Microsoft Azure AD Connect** 를 선택한 다음 **제거** 를 선택합니다.
1. **프로그램 및 기능** 대화 상자에서 **예** 를 선택합니다.
1. **Azure AD Connect 제거** 창에서 **제거** 를 선택합니다.
1. Azure AD Connect가 제거되면 **Azure AD Connect 제거** 창에서 **끝내기** 를 선택합니다.

#### <a name="task-2-disable-directory-synchronization-in-azure"></a>작업 2: Azure에서 디렉터리 동기화를 사용하지 않도록 설정

1. **SEA-ADM1** 에서 **Windows PowerShell** 콘솔 창으로 전환합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 실행하고 Enter를 눌러 Azure AD용 Microsoft Online 모듈을 설치합니다.

   ```powershell
   Install-Module -Name MSOnline
   ```
1. NuGet 공급자를 설치하라는 메시지가 표시되면 **Y** 를 입력한 다음 Enter를 누릅니다.
1. 신뢰할 수 없는 리포지토리에서 모듈을 설치하라는 메시지가 표시되면 **A** 를 입력한 다음 Enter를 누릅니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter를 눌러 Azure AD 자격 증명을 변수에 저장합니다.

   ```powershell
   $msolCred = Get-Credential
   ```
1. **Windows PowerShell 자격 증명 요청** 대화 상자에서 연습 1에서 만든 Azure AD 전역 관리자 사용자 계정의 자격 증명을 입력한 다음 **확인** 을 선택합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력하고 Enter를 눌러 Azure AD 테넌트를 인증합니다.

   ```powershell
   Connect-MsolService -Credential $msolCred
   ```
1. 다음 명령을 입력하고 Enter를 눌러 디렉터리 동기화를 사용하지 않도록 설정합니다.

   ```powershell
   Set-MsolDirSyncEnabled -EnableDirSync $false
   ```
1. 확인하라는 메시지가 표시되면 **Y** 를 입력한 다음 Enter를 누릅니다.
