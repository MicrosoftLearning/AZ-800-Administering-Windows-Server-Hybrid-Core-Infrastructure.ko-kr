---
lab:
  title: '랩: 하이브리드 시나리오에서 Windows Admin Center 사용'
  type: Answer Key
  module: 'Module 4: Facilitating hybrid management'
ms.openlocfilehash: 421d45b0bd0c9453ad44300759473539e7a82c03
ms.sourcegitcommit: d34dce53481b0263d0ff82913b3f49cb173d5c06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2022
ms.locfileid: "147039448"
---
# <a name="lab-answer-key-using-windows-admin-center-in-hybrid-scenarios"></a>랩 정답 키: 하이브리드 시나리오에서 Windows Admin Center 사용

## <a name="exercise-1-provisioning-azure-vms-running-windows-server"></a>연습 1: Windows Server에서 실행하는 Azure VM 프로비전

#### <a name="task-1-create-an-azure-resource-group-by-using-an-azure-resource-manager-template"></a>작업 1: Azure Resource Manager 템플릿을 사용하여 Azure 리소스 그룹 만들기

1. **SEA-ADM1** 에 연결하고, 필요한 경우 **Pa55w.rd** 암호를 사용해 **CONTOSO\\Administrator** 로 로그인합니다.
1. **SEA-ADM1** 에서 Microsoft Edge를 시작하고 [Azure Portal](https://portal.azure.com)로 이동한 다음, 이 랩에서 사용할 구독에서 소유자 역할이 있는 사용자 계정의 자격 증명을 사용하여 로그인합니다.
1. Azure Portal에서 검색 텍스트 상자 바로 옆의 도구 모음 아이콘을 선택하여 Cloud Shell 창을 엽니다.
1. **Bash** 와 **PowerShell** 중에서 선택하라는 메시지가 표시되면 **PowerShell** 을 선택합니다.

   >**참고:** Cloud Shell을 처음 시작하는 경우 **탑재된 스토리지가 없음** 메시지가 표시되면 이 랩에서 사용 중인 구독을 선택한 후 **스토리지 만들기** 를 선택합니다.

1. Cloud Shell 창의 도구 모음에서 **파일 업로드/다운로드** 아이콘을 선택하고 드롭다운 메뉴에서 **업로드** 를 선택한 다음 **C:\\Labfiles\\Lab04\\L04-sub_template.json** 파일을 Cloud Shell 홈 디렉터리에 업로드합니다.
1. Cloud Shell 창에서 다음 명령을 실행하여 이 랩에서 프로비전하는 리소스를 포함할 리소스 그룹을 만듭니다. (`<Azure region>` 자리 표시자를 **eastus** 같은, Azure 가상 머신을 배포할 수 있는 Azure 지역의 이름으로 바꿉니다.)

   >**참고**: 이 랩은 미국 동부를 사용하여 테스트 및 확인되었으므로 해당 지역을 사용해야 합니다. Azure VM을 프로비전할 수 있는 Azure 지역을 식별하는 일반적인 방법은 [해당 지역에서 제공되는 Azure 크레딧 제안 찾기](https://aka.ms/regions-offers)를 참조하세요.

   ```powershell
   $location = '<Azure region>'
   $rgName = 'AZ800-L0401-RG'
   New-AzSubscriptionDeployment `
     -Location $location `
     -Name az800l04subDeployment `
     -TemplateFile $HOME/L04-sub_template.json `
     -rgLocation $location `
     -rgName $rgName
   ```

#### <a name="task-2-create-an-azure-vm-by-using-an-azure-resource-manager-template"></a>작업 2: Azure Resource Manager 템플릿을 사용하여 Azure VM 만들기

1. Cloud Shell 창에서 Azure Resource Manager 템플릿인 **C:\\Labfiles\\Lab04\\L04-rg_template.json** 과 대응하는 Azure Resource Manager 매개 변수 파일인 **C:\\Labfiles\\Lab04\\L04-rg_template.parameters.json** 을 업로드합니다.
1. Cloud Shell 창에서 다음 명령을 실행하여 이 랩에서 사용할 Windows Server를 실행하는 Azure VM을 배포합니다.

   ```powershell
   New-AzResourceGroupDeployment `
     -Name az800l04rgDeployment `
     -ResourceGroupName $rgName `
     -TemplateFile $HOME/L04-rg_template.json `
     -TemplateParameterFile $HOME/L04-rg_template.parameters.json
   ```

   >**참고**: 배포가 완료될 때까지 기다린 후 다음 연습을 진행하세요. 배포는 5분 정도 걸립니다.

1. Azure Portal에서 Cloud Shell 창을 닫습니다.
1. Azure Portal에서 도구 모음의 **리소스, 서비스 및 문서 검색** 텍스트 상자를 사용하여 **az800l04-vnet** 가상 네트워크를 검색하고 선택합니다.
1. **az800l04-vnet** 페이지에서 **서브넷** 을 선택한 다음 **서브넷** 페이지에서 **+ 게이트웨이 서브넷** 을 선택합니다.
1. **서브넷 추가** 페이지에서 **서브넷 주소 범위** 를 **10.4.3.224/27** 로 설정한 다음 **저장** 을 선택하여 **GatewaySubnet** 을 만듭니다.

## <a name="exercise-2-implementing-hybrid-connectivity-by-using-the-azure-network-adapter"></a>연습 2: Azure 네트워크 어댑터를 사용하여 하이브리드 연결 구현

#### <a name="task-1-register-windows-admin-center-with-azure"></a>작업 1: Windows Admin Center를 Azure에 등록

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

   >**참고**: 링크가 작동하지 않는다면 **SEA-ADM1** 에서 **WindowsAdminCenter.msi** 파일로 이동하여 상황에 맞는 메뉴를 연 다음 **복구** 를 선택하세요. 복구가 완료되면 Microsoft Edge를 새로 고칩니다. 
   
1. 메시지가 표시되면 **Windows 보안** 대화 상자에 다음 자격 증명을 입력한 다음 **확인** 을 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. **모든 연결** 페이지에서 **sea-adm1.contoso.com** 항목을 선택합니다. 
1. Windows Admin Center에서 **네트워크** 를 선택하고 **작업** 을 선택한 다음 **+ Azure 네트워크 어댑터 추가(미리 보기)** 를 선택합니다.

   > **참고**: 화면 해상도에 따라 **작업** 메뉴를 사용할 수 없는 경우 **줄임표** 아이콘을 선택해야 할 수 있습니다.

1. 메시지가 표시되면 **Azure 네트워크 어댑터 추가** 창에서 **Azure에 Windows Admin Center 등록** 을 선택합니다.

   >**참고**: Windows Admin Center 내의 **설정** 페이지에 Azure 창이 자동으로 표시됩니다.

1. Windows Admin Center에 표시된 Azure 창의 **설정** 페이지에서 **등록** 을 선택합니다.
1. **Windows Admin Center에서 Azure 시작** 창에서 **복사** 를 선택하여 등록 절차의 단계 목록에 표시된 코드를 복사합니다. 
1. 등록 절차의 단계 목록에서 **코드 입력** 링크를 선택합니다.

   >**참고**: Microsoft Edge 창에 **코드 입력** 페이지가 표시되는 다른 탭이 열립니다.

1. **코드 입력** 텍스트 상자에서 복사한 코드를 클립보드에 붙여넣은 후 **다음** 을 선택합니다.
1. **로그인** 페이지에 이전 연습에서 Azure 구독에 로그인하는 데 사용한 것과 같은 사용자 이름을 입력하고 **다음** 을 선택한 후 해당 암호를 입력하고 **로그인** 을 선택합니다.
1. **Windows Admin Center에 로그인하시겠습니까?** 라는 메시지가 표시되면 **계속** 을 선택합니다.
1. Windows Admin Center에서 로그인이 성공했는지 확인하고 Microsoft Edge 창에 새로 열린 탭을 닫습니다.
1. **Windows Admin Center에서 Azure 시작** 창에서 **Azure Active Directory 애플리케이션** 이 **새로 만들기** 로 설정되어 있는지 확인하고 **연결** 을 선택합니다.
1. 등록 절차의 단계 목록에서 **로그인** 을 선택합니다. 그러면 **요청된 권한** 이라는 레이블이 지정된 팝업 창이 열립니다.
1. **요청된 권한** 팝업 창에서 **조직 대신 동의** 를 선택한 다음 **수락** 을 선택합니다.

#### <a name="task-2-create-an-azure-network-adapter"></a>작업 2: Azure 네트워크 어댑터 만들기

1. **SEA-ADM1** 에서 Windows Admin Center가 표시되는 Microsoft Edge 창으로 돌아가 **sea-svr2.contoso.com** 페이지로 이동한 다음 **네트워크** 를 선택합니다.
1. Windows Admin Center의 **네트워크** 페이지에 있는 **작업** 메뉴에서 **+ Azure 네트워크 어댑터 추가(미리 보기)** 항목을 다시 선택합니다.
1. **네트워크 어댑터 추가** 설정 창에서 다음 설정을 지정한 다음 **만들기** 를 선택합니다(다른 설정은 기본값으로 유지).

   |설정|값|
   |---|---|
   |Subscription|이 랩에서 사용 중인 Azure 구독의 이름|
   |Location|eastus|
   |가상 네트워크|az800l04-vnet|
   |게이트웨이 서브넷|10.4.3.224/27|
   |게이트웨이 SKU|VpnGw1|
   |클라이언트 주소 공간|192.168.0.0/24|
   |인증 인증서|자동 생성된 자체 서명된 루트 및 클라이언트 인증서|

1. **SEA-ADM1** 에서, Azure Portal이 표시된 Microsoft Edge 창의 도구 모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **가상 네트워크 게이트웨이** 를 검색하고 선택합니다.
1. **가상 네트워크 게이트웨이** 페이지에서 **새로 고침** 을 선택하고 **WAC-Created-vpngw-96** 으로 시작하는 이름의 새 항목이 가상 네트워크 게이트웨이 목록에 표시되는지 확인합니다.

>**참고**: Azure 가상 네트워크 게이트웨이 프로비전은 최대 45분이 소요됩니다. 프로비전이 완료될 때까지 기다리지 말고 다음 연습을 진행하세요.

## <a name="exercise-3-deploying-windows-admin-center-gateway-in-azure"></a>연습 3: Azure에서 Windows Admin Center 게이트웨이 배포

#### <a name="task-1-install-windows-admin-center-gateway-in-azure"></a>작업 1: Azure에 Windows Admin Center 게이트웨이 설치

1. **SEA-ADM1** 에서 Azure Portal이 표시된 브라우저 창으로 전환합니다.
1. Azure Portal로 돌아가서 **Cloud Shell** 아이콘을 선택하여 Cloud Shell 창을 엽니다.
1. Cloud Shell 창의 도구 모음에서 **파일 업로드/다운로드** 아이콘을 선택하고 드롭다운 메뉴에서 **업로드** 를 선택한 다음 **C:\\Labfiles\\Lab04\\Deploy-WACAzVM.ps1** 파일을 Cloud Shell 홈 디렉터리에 업로드합니다.
1. Cloud Shell 창에서 다음 명령을 실행하여 Windows Admin Center 프로비전 스크립트에서 사용하는 **AzureRm** PowerShell cmdlet의 호환성을 사용하도록 설정합니다.

   ```powershell
   Enable-AzureRmAlias -Scope Process
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 Windows Admin Center 프로비전 스크립트를 실행하는 데 필요한 변수의 값을 설정합니다.

   ```powershell
   $rgName = 'AZ800-L0401-RG'
   $vnetName = 'az800l04-vnet'
   $nsgName = 'az800l04-web-nsg'
   $subnetName = 'subnet1'
   $location = 'eastus'
   $pipName = 'wac-public-ip'
   $size = 'Standard_D2s_v3'
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 스크립트 매개 변수 변수를 설정합니다.

   ```powershell
   $scriptParams = @{
     ResourceGroupName = $rgName
     Name = 'az800l04-vmwac'
     VirtualNetworkName = $vnetName
     SubnetName = $subnetName
     GenerateSslCert = $true
     size = $size
     PublicIPAddressName = $pipname
   }
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 PowerShell 원격에 대한 인증서 확인을 사용하지 않도록 설정합니다. 신뢰할 수 없는 리포지토리에서 설치를 확인하라는 메시지가 표시되면 **A** 를 입력하고 Enter 키를 누릅니다.

   ```powershell
   Install-Module -Name pswsman
   Disable-WSManCertVerification -All
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 프로비전 스크립트를 시작합니다.

   ```powershell
   ./Deploy-WACAzVM.ps1 @scriptParams
   ```

1. 로컬 관리자 계정의 이름을 입력하라는 메시지가 표시되면 **Student** 를 입력합니다.
1. 로컬 관리자 계정의 암호를 입력하라는 메시지가 표시되면 **Pa55w.rd1234** 를 입력합니다.

   >**참고**: 프로비전 스크립트가 완료될 때까지 기다리세요. 5분 정도 걸릴 수 있습니다.

1. 스크립트가 성공적으로 완료되었는지 확인하고, Windows Admin Center 설치를 호스트하는 Azure VM의 정규화된 이름이 포함된 URL을 제공하는 최종 메시지를 확인합니다.

   >**참고**: Azure VM의 정규화된 이름을 기록하세요. 이 랩의 후반부에서 필요합니다.

1. Cloud Shell 창을 닫습니다.

#### <a name="task-2-review-results-of-the-script-provisioning"></a>작업 2: 스크립트 프로비전 결과 검토

1. Azure Portal의 도구 모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **리소스 그룹** 을 검색하여 선택한 다음, **리소스 그룹** 페이지에서 **AZ800-L0401-RG** 항목을 선택합니다.
1. **AZ800-L0401-RG** 페이지의 **개요** 페이지에서 Azure VM **az800l04-vmwac** 를 포함한 리소스 목록을 검토합니다.
1. 리소스 목록에서 Azure VM **az800l04-vmwac** 항목을 선택한 다음, **az800l04-vmwac** 페이지에서 **네트워킹** 을 선택합니다.
1. **az800l04-vmwac | 네트워킹** 페이지의 **인바운드 포트 규칙** 탭에서, TCP 포트 5986에서의 연결을 허용하는 인바운드 포트 규칙과 TCP 포트 443에서의 연결을 허용하는 인바운드 규칙을 나타내는 항목을 기록해 둡니다.

## <a name="exercise-4-verifying-functionality-of-the-windows-admin-center-gateway-in-azure"></a>연습 4: Azure에서 Windows Admin Center 게이트웨이 기능 확인

#### <a name="task-1-connect-to-the-windows-admin-center-gateway-running-in-azure-vm"></a>작업 1: Azure VM에서 실행하는 Windows Admin Center 게이트웨이에 연결

1. **SEA-ADM1** 에서 Microsoft Edge를 시작하고 이전 연습에서 식별한 Windows Admin Center 설치를 호스트하는 대상 Azure VM의 정규화된 이름을 포함하는 URL로 이동합니다.
1. Microsoft Edge 창에서 **연결이 프라이빗이 아닙니다.** 메시지를 무시하고 **고급** 을 선택한 다음 **계속** 텍스트로 시작하는 링크를 선택합니다.
1. 메시지가 표시되면 **이 사이트에 액세스하려면 로그인하세요.** 대화 상자에서 **Student** 사용자 이름과 **Pa55w.rd1234** 암호로 로그인합니다.
1. Windows Admin Center의 **모든 연결** 창에서 **az800l04-vmwac [Gateway]** 를 선택합니다.
1. Windows Admin Center의 개요 창을 확인합니다.

#### <a name="task-2-enable-powershell-remoting-on-an-azure-vm"></a>작업 2: Azure VM에서 PowerShell 원격 작업 사용

1. **SEA-ADM1** 에서, Azure Portal이 표시된 Microsoft Edge 창으로 전환한 다음 도구 모음의 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **가상 머신** 을 검색하고 선택합니다.
1. **가상 머신** 페이지에서 **az800l04-vm0** 을 선택합니다.
1. **az800l04-vm0** 페이지의 **작업** 섹션에서 **명령 실행** 을 선택한 다음 **RunPowerShellScript** 를 선택합니다.
1. Windows 원격 관리를 사용하지 않도록 설정된 경우 **명령 스크립트 실행** 페이지의 **PowerShell 스크립트** 섹션에서 다음 명령을 입력한 다음 **실행** 을 선택하여 Windows 원격 관리를 사용하도록 설정합니다.

   ```powershell
   winrm quickconfig -quiet
   ```

1. **PowerShell 스크립트** 섹션에서 이전 단계에서 입력한 텍스트를 다음 명령으로 바꾼 다음 **실행** 을 선택하여 Windows 원격 관리 인바운드 포트를 엽니다.

   ```powershell
   Set-NetFirewallRule -Name WINRM-HTTP-In-TCP-PUBLIC -RemoteAddress Any
   ```

1. **PowerShell 스크립트** 섹션에서 이전 단계에서 입력한 텍스트를 다음 명령으로 바꾼 다음 **실행** 을 선택하여 PowerShell 원격을 사용하도록 설정합니다.

   ```powershell
   Enable-PSRemoting -Force -SkipNetworkProfileCheck
   ```

#### <a name="task-3-connect-to-an-azure-vm-by-using-the-windows-admin-center-gateway-running-in-azure-vm"></a>작업 3: Azure VM에서 실행하는 Windows Admin Center 게이트웨이를 사용하여 Azure VM에 연결

1. **SEA-ADM1** 에서, **az800l04-vmwac** Azure VM에서 실행되는 Windows Admin Center 게이트웨이의 인터페이스가 표시된 Microsoft Edge 창에서 **Windows Admin Center** 를 선택합니다.
1. **모든 연결** 페이지에서 **+ 추가** 를 선택합니다.
1. **리소스 추가 또는 만들기** 페이지의 **서버** 섹션에서 **추가** 를 선택합니다.
1. **서버 이름** 텍스트 상자에 **az800l04-vm0** 을 입력합니다.
1. **이 연결 옵션에 다른 계정 사용** 옵션을 선택하고 **Student** 사용자 이름과 **Pa55w.rd1234** 암호를 제공한 다음 **자격 증명을 사용하여 추가** 를 선택합니다.
1. 연결 목록에서 **az800l04-vm0** 을 선택합니다.
1. Azure VM에 성공적으로 연결되면 Windows Admin Center에서 **az800l04-vmwac** Azure VM의 개요 창을 검토합니다.

## <a name="exercise-5-deprovisioning-the-azure-environment"></a>연습 5: Azure 환경 프로비전 해제

#### <a name="task-1-start-a-powershell-session-in-cloud-shell"></a>작업 1: Cloud Shell에서 PowerShell 세션 시작

1. **SEA-ADM1** 에서 Azure Portal이 표시된 Microsoft Edge 창으로 전환합니다.
1. Azure Portal이 표시된 Microsoft Edge 창에서 Cloud Shell 아이콘을 선택하여 Cloud Shell 창을 엽니다.

#### <a name="task-2-identify-all-azure-resources-provisioned-in-the-lab"></a>작업 2: 랩에서 프로비전한 모든 Azure 리소스 식별

1. Cloud Shell 창에서 다음 명령을 실행하여 이 랩 전체에서 만든 리소스 그룹을 모두 나열합니다.

   ```powershell
   Get-AzResourceGroup -Name 'AZ800-L040*'
   ```

1. 다음 명령을 실행하여 이 랩 전체에서 만든 리소스 그룹을 모두 삭제합니다.

   ```powershell
   Get-AzResourceGroup -Name 'AZ800-L040*' | Remove-AzResourceGroup -Force -AsJob
   ```

   >**참고**: 이 명령은 -AsJob 매개 변수에 의해 결정되어 비동기로 실행되므로, 동일한 PowerShell 세션 내에서 이 명령을 실행한 직후 다른 PowerShell 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지는 몇 분 정도 걸립니다.
