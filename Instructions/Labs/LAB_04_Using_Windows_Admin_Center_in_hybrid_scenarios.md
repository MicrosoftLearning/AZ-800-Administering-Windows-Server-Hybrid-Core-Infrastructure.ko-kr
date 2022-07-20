---
lab:
  title: '랩: 하이브리드 시나리오에서 Windows Admin Center 사용'
  module: 'Module 4: Facilitating hybrid management'
ms.openlocfilehash: a39562df5131e07d2cb50634629bbb40a15f82c8
ms.sourcegitcommit: d34dce53481b0263d0ff82913b3f49cb173d5c06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2022
ms.locfileid: "147039388"
---
# <a name="lab-using-windows-admin-center-in-hybrid-scenarios"></a>랩: 하이브리드 시나리오에서 Windows Admin Center 사용

## <a name="scenario"></a>시나리오

관리되는 시스템의 위치에 관계없이 일관된 운영 및 관리 모델과 관련된 문제를 해결하기 위해, 온-프레미스 및 Microsoft Azure VM(Virtual Machine)에서 실행하는 다양한 버전의 Windows Server 운영 체제가 포함된 하이브리드 환경에서 Windows Admin Center의 기능을 테스트합니다.

여러분의 목표는 관리되는 시스템의 위치에 관계없이 Windows Admin Center를 일관된 방식으로 사용할 수 있는지를 확인하는 것입니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음을 수행할 수 있습니다.

- Azure 네트워크 어댑터를 사용하여 하이브리드 연결을 테스트합니다.
- Azure에서 Windows Admin Center 게이트웨이를 배포합니다.
- Azure에서 Windows Admin Center 게이트웨이 기능을 확인합니다.

## <a name="estimated-time-90-minutes"></a>예상 시간: 90분

## <a name="lab-setup"></a>랩 설정

가상 머신: **AZ-800T00A-SEA-DC1** 및 **AZ-800T00A-ADM1** 을 실행해야 합니다. 다른 VM을 실행할 수도 있지만 이 랩에서는 필요하지 않습니다.

> **참고**: **AZ-800T00A-SEA-DC1** 및 **AZ-800T00A-SEA-ADM1** VM은 **SEA-DC1** 및 **SEA-ADM1** 의 설치를 호스트합니다.

1. **SEA-ADM1** 을 선택합니다.
1. 다음 자격 증명을 사용하여 로그인합니다.

   - 사용자 이름: **Administrator**
   - 암호: **Pa55w.rd**
   - 도메인: **CONTOSO**

이 랩에서는 사용 가능한 VM 환경과 Azure 구독을 사용합니다. 랩을 시작하기 전에 Azure 구독 및 구독에 Owner 또는 Contributor 역할이 있는 사용자 계정과, 해당 구독과 연결된 Azure AD(Azure Active Directory) 테넌트에 전역 관리자 역할이 있는지 확인합니다.

## <a name="exercise-1-provisioning-azure-vms-running-windows-server"></a>연습 1: Windows Server에서 실행하는 Azure VM 프로비전

### <a name="scenario"></a>시나리오

온-프레미스 서버와 Azure 가상 네트워크 간에 하이브리드 연결을 설정할 수 있는지 확인해야 합니다. 먼저 Azure Resource Manager 템플릿을 사용하여 Windows Server를 실행하는 Azure VM을 프로비전해야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Azure Resource Manager 템플릿을 사용하여 Azure 리소스 그룹을 만듭니다.
1. Azure Resource Manager 템플릿을 사용하여 Azure VM을 만듭니다.

#### <a name="task-1-create-an-azure-resource-group-by-using-an-azure-resource-manager-template"></a>작업 1: Azure Resource Manager 템플릿을 사용하여 Azure 리소스 그룹 만들기

1. **SEA-ADM1** 에서 Microsoft Edge를 시작하고 Azure Portal로 이동한 다음 Azure 자격 증명으로 인증합니다.
1. Azure Portal의 Cloud Shell 창에서 PowerShell 세션을 엽니다.
1. **C:\\LabfilesLab04L04-sub_template.json\\\\** 파일을 Cloud Shell 홈 디렉터리에 업로드합니다.
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
1. Azure Portal에서 IP 주소 범위가 **10.4.3.224/27** 인 **GatewaySubnet** 을 **az800l04-vnet** 가상 네트워크에 추가합니다.

## <a name="exercise-2-implementing-hybrid-connectivity-by-using-the-azure-network-adapter"></a>연습 2: Azure 네트워크 어댑터를 사용하여 하이브리드 연결 구현

### <a name="scenario"></a>시나리오

이전 연습에서 프로비전한 온-프레미스 서버와 Azure VM 간에 하이브리드 연결을 설정할 수 있는지 확인해야 합니다. 이 작업에는 Windows Admin Center의 Azure 네트워크 어댑터 기능을 사용합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Windows Admin Center를 Azure에 등록합니다.
1. Azure 네트워크 어댑터를 만듭니다.

#### <a name="task-1-register-windows-admin-center-with-azure"></a>작업 1: Windows Admin Center를 Azure에 등록

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

   >**참고**: 링크가 작동하지 않는다면 **SEA-ADM1** 에서 **WindowsAdminCenter.msi** 파일로 이동하여 상황에 맞는 메뉴를 연 다음 **복구** 를 선택하세요. 복구가 완료되면 Microsoft Edge를 새로 고칩니다. 

1. 메시지가 표시되면 **Windows 보안** 대화 상자에 다음 자격 증명을 입력한 다음 **확인** 을 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. Windows Admin Center 페이지에서 Azure 네트워크 어댑터를 추가합니다.
1. 메시지가 표시되면 이전 연습에서 사용한 Azure 구독에 Windows Admin Center를 등록합니다.

#### <a name="task-2-create-an-azure-network-adapter"></a>작업 2: Azure 네트워크 어댑터 만들기

1. **SEA-ADM1** 에서 Windows Admin Center를 표시하는 Microsoft Edge를 창에서 Azure 네트워크 어댑터를 다시 만듭니다.
1. 다음 설정을 사용하여 Azure 네트워크 어댑터를 만듭니다.

   |설정|값|
   |---|---|
   |Subscription|이 랩에서 사용 중인 Azure 구독의 이름|
   |Location|eastus|
   |가상 네트워크|az800l04-vnet|
   |게이트웨이 서브넷|10.4.3.224/27|
   |게이트웨이 SKU|VpnGw1|
   |클라이언트 주소 공간|192.168.0.0/24|
   |인증 인증서|자동 생성된 자체 서명된 루트 및 클라이언트 인증서|

1. **SEA-ADM1** 에서 Azure Portal을 표시하는 Microsoft Edge를 창으로 전환하고 이름이 **WAC-Created-vpngw-** 로 시작하는 새 가상 네트워크 게이트웨이가 프로비전되고 있는지 확인합니다.

   >**참고**: Azure 가상 네트워크 게이트웨이 프로비전은 최대 45분이 소요됩니다. 프로비전이 완료될 때까지 기다리지 말고 다음 연습을 진행하세요.

## <a name="exercise-3-deploying-windows-admin-center-gateway-in-azure"></a>연습 3: Azure에서 Windows Admin Center 게이트웨이 배포

### <a name="scenario"></a>시나리오

Windows Server OS를 실행하는 Azure VM을 Windows Admin Center를 사용하여 관리하는 기능을 평가해야 합니다. 이렇게 하려면 먼저 이 랩의 첫 번째 연습에서 구현한 Azure 가상 네트워크에 Windows Admin Center 게이트웨이를 설치해야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Azure에 Windows Admin Center 게이트웨이를 설치합니다.
1. 스크립트 프로비전 결과를 검토합니다.

#### <a name="task-1-install-windows-admin-center-gateway-in-azure"></a>작업 1: Azure에 Windows Admin Center 게이트웨이 설치

1. **SEA-ADM1** 에서 Azure Portal이 표시된 브라우저 창으로 전환합니다.
1. Azure Portal의 Cloud Shell 창에서 PowerShell 세션을 시작합니다.
1. Cloud Shell 창에서 **C:\\Labfiles\\Lab04\\Deploy-WACAzVM.ps1** 파일을 Cloud Shell 홈 디렉터리에 업로드합니다.
1. Cloud Shell 창에서 다음 명령을 실행하여 Windows Admin Center 프로비전 스크립트에서 사용하는 **AzureRm** PowerShell cmdlet의 호환성을 사용하도록 설정합니다.

   ```powershell
   Enable-AzureRmAlias -Scope Process
   ```
1. 다음 명령을 실행하여 Windows Admin Center 프로비저닝 스크립트를 실행하는 데 필요한 변수 값을 설정합니다(`<Azure region>` 자리 표시자를 이 랩의 앞부분에서 리소스를 배포하는 Azure 지역 이름(예: **eastus**)으로 바꿉니다.)

   ```powershell
   $rgName = 'AZ800-L0401-RG'
   $vnetName = 'az800l04-vnet'
   $nsgName = 'az800l04-web-nsg'
   $subnetName = 'subnet1'
   $location = '<Azure region>'
   $pipName = 'wac-public-ip'
   $size = 'Standard_D2s_v3'
   ```
1. 다음 명령을 실행하여 스크립트 매개 변수 변수를 설정합니다.

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
1. 다음 명령을 실행하여 PowerShell 원격에 대한 인증서 확인을 사용하지 않도록 설정합니다(첫 번째 명령 다음에 메시지가 표시되면 **A** 를 입력하고 Enter 키를 누릅니다).

   ```powershell
   install-module pswsman
   Disable-WSManCertVerification -All
   ```
1. 다음 명령을 실행하여 프로비전 스크립트를 시작합니다.

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

1. Azure Portal에서 **AZ800-L0401-RG** 리소스 그룹의 페이지로 이동합니다.
1. **AZ800-L0401-RG** 페이지의 **개요** 페이지에서 Azure VM **az800l04-vmwac** 를 포함한 리소스 목록을 검토합니다.
1. **az800l04-vmwac | 네트워킹** 페이지의 **인바운드 포트 규칙** 탭에서, TCP 포트 5986에서의 연결을 허용하는 인바운드 포트 규칙과 TCP 포트 443에서의 연결을 허용하는 인바운드 규칙을 나타내는 항목을 기록해 둡니다.

## <a name="exercise-4-verifying-functionality-of-the-windows-admin-center-gateway-in-azure"></a>연습 4: Azure에서 Windows Admin Center 게이트웨이 기능 확인

### <a name="scenario"></a>시나리오

필요한 구성 요소가 모두 준비되면, 이 랩의 첫 번째 연습에서 프로비전한 Azure 가상 네트워크에 배포된 Azure VM을 대상으로 WAC 기능을 테스트합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Azure VM에서 실행하는 Windows Admin Center 게이트웨이에 연결합니다.
1. Azure VM에서 PowerShell 원격 작업을 사용하도록 설정합니다.
1. Azure VM에서 실행하는 Windows Admin Center 게이트웨이를 사용하여 Azure VM에 연결합니다.

#### <a name="task-1-connect-to-the-windows-admin-center-gateway-running-in-azure-vm"></a>작업 1: Azure VM에서 실행하는 Windows Admin Center 게이트웨이에 연결

1. **SEA-ADM1** 에서 Microsoft Edge를 시작하고 이전 연습에서 식별한 Azure VM에서 실행하는 Windows Admin Center 게이트웨이를 연결합니다.
1. 메시지가 표시되면 **Student** 사용자 이름과 **Pa55w.rd1234** 암호를 이용해 로그인합니다.
1. Windows Admin Center의 모든 연결 창에서 **az800l04-vmwac [Gateway]** 를 선택합니다.
1. Windows Admin Center의 개요 창을 확인합니다.

#### <a name="task-2-enable-powershell-remoting-on-an-azure-vm"></a>작업 2: Azure VM에서 PowerShell 원격 작업 사용

1. **SEA-ADM1** 에서 Azure Portal을 표시하는 Microsoft Edge를 창에서 **az800l04-vm0** Azure VM의 페이지로 이동합니다.
1. **az800l04-vm0** 페이지에서 **명령 실행** 기능으로 다음 명령을 실행하여 사용하지 않도록 설정된 경우 Windows 원격 관리를 사용하도록 설정합니다.

   ```powershell
   winrm quickconfig -quiet
   ```

1. **명령 실행** 기능으로 다음 명령을 실행하여 Windows 원격 관리 인바운드 포트를 엽니다.

   ```powershell
   Set-NetFirewallRule -Name WINRM-HTTP-In-TCP-PUBLIC -RemoteAddress Any
   ```

1. **명령 실행** 기능으로 다음 명령을 실행하여 PowerShell 원격 작업을 사용하도록 설정합니다.

   ```powershell
   Enable-PSRemoting -Force -SkipNetworkProfileCheck
   ```

#### <a name="task-3-connect-to-an-azure-vm-by-using-the-windows-admin-center-gateway-running-in-azure-vm"></a>작업 3: Azure VM에서 실행하는 Windows Admin Center 게이트웨이를 사용하여 Azure VM에 연결

1. **SEA-ADM1** 에 있는, **az800l04-vmwac** Azure VM에서 실행하는 Windows Admin Center 게이트웨이의 인터페이스를 표시하는 Microsoft Edge를 창에서 해당 이름을 사용하여 Azure VM **az800l04-vm0** 에 대한 연결을 추가합니다.
1. 메시지가 표시되면 **Student** 사용자 이름과 **Pa55w.rd1234** 암호를 사용하여 인증합니다.
1. VM에 성공적으로 연결되면 Windows Admin Center에서 **az800l04-vmwac** Azure VM의 개요 창을 검토합니다.

## <a name="exercise-5-deprovisioning-the-azure-environment"></a>연습 5: Azure 환경 프로비전 해제

### <a name="scenario"></a>시나리오

Azure 관련 요금을 최소화하기 위해 이 랩 전체에서 프로비전한 Azure 리소스의 프로비전을 해제합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Cloud Shell에서 PowerShell 세션을 시작합니다.
1. 랩에서 프로비전한 모든 Azure 리소스를 식별합니다.

#### <a name="task-1-start-a-powershell-session-in-cloud-shell"></a>작업 1: Cloud Shell에서 PowerShell 세션 시작

1. **SEA-ADM1** 에서 Azure Portal이 표시된 Microsoft Edge 창으로 전환합니다.
1. Azure Portal의 Cloud Shell 창에서 PowerShell 세션을 엽니다.

#### <a name="task-2-identify-all-azure-resources-provisioned-in-the-lab"></a>작업 2: 랩에서 프로비전한 모든 Azure 리소스 식별

1. Cloud Shell 창에서 다음 명령을 실행하여 이 랩 전체에서 만든 리소스 그룹을 모두 나열합니다.

   ```powershell
   Get-AzResourceGroup -Name 'az800l04*'
   ```

1. 다음 명령을 실행하여 이 랩 전체에서 만든 리소스 그룹을 모두 삭제합니다.

   ```powershell
   Get-AzResourceGroup -Name 'az800l04*' | Remove-AzResourceGroup -Force -AsJob
   ```

   >**참고**: 명령은 -AsJob 매개 변수에 의해 결정된 대로 비동기적으로 실행됩니다. 따라서 동일한 PowerShell 세션에서 즉시 다른 PowerShell 명령을 실행할 수 있지만, 리소스 그룹이 실제로 제거되려면 몇 분 정도 걸립니다.

### <a name="results"></a>결과

이 랩을 완료하면 Contoso의 관리 효율성 및 보안 요구 사항을 충족하는 방식으로 Windows Server를 실행하는 Azure VM을 배포하고 구성하게 됩니다.

### <a name="prepare-for-the-next-module"></a>다음 모듈 준비

다음 모듈에 대한 준비가 끝나면 랩을 종료하세요.
