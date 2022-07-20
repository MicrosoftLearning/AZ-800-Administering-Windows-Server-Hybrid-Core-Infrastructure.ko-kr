---
lab:
  title: '랩: Azure VM에서 Windows Server 배포 및 구성'
  module: 'Module 6: Deploying and Configuring Azure VMs'
ms.openlocfilehash: bbf7d0657532d3b162ac8c366cc37da11ae3c32c
ms.sourcegitcommit: d34dce53481b0263d0ff82913b3f49cb173d5c06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2022
ms.locfileid: "147039433"
---
# <a name="lab-deploying-and-configuring-windows-server-on-azure-vms"></a>랩: Azure VM에서 Windows Server 배포 및 구성

## <a name="scenario"></a>시나리오

현재 인프라와 관련된 문제를 해결해야 합니다. 운영 모델이 오래되었고, 자동화가 제한적으로 사용되며, 정보 보안 팀은 Windows Server 기반 워크로드를 실행하는 Azure VM에 추가적인 컨트롤을 적용해야 한다는 점을 우려하고 있습니다. 여러분은 Windows Server를 실행하는 Azure VM을 위해 자동화된 배포 및 구성 프로세스를 개발하고 구현하기로 했습니다.

이 프로세스에는 Azure VM 확장을 통한 ARM(Azure Resource Manager) 템플릿 및 OS 구성이 동반됩니다. 또한 이 프로세스는 온-프레미스 시스템에 이미 적용된 메커니즘에 추가 보안 보호 메커니즘(AppLocker를 통한 애플리케이션 허용 목록, 파일 무결성 검사, 적응형 네트워크/DDoS 보호 등)을 통합합니다. 그리고 JIT 기능을 활용하여 Azure VM에 대한 관리 액세스를 런던 본사와 연결된 공용 IP 주소 범위로 제한합니다.

목표는 관리 효율성 및 보안 요구 사항을 충족하는 방식으로 Windows Server를 실행하는 Azure VM을 배포하고 구성하는 것입니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음을 수행할 수 있습니다.

- Azure VM 배포용 ARM 템플릿에 권한을 부여합니다.
- VM 확장 기반 구성을 포함하도록 ARM 템플릿을 수정합니다.
- Windows Server에서 실행하는 Azure VM을 ARM 템플릿을 사용하여 배포합니다.
- Windows Server에서 실행하는 Azure VM에 대한 관리자 액세스를 구성합니다.
- Azure VM에서 Windows Server 보안을 구성합니다.
- Azure 환경의 프로비전을 해제합니다.

## <a name="estimated-time-90-minutes"></a>예상 시간: 90분

## <a name="lab-setup"></a>랩 설정

가상 머신: **AZ-800T00A-SEA-DC1** 및 **AZ-800T00A-ADM1** 을 실행해야 합니다. 다른 VM을 실행할 수도 있지만 이 랩에서는 필요하지 않습니다.

> **참고**: **AZ-800T00A-SEA-DC1** 및 **AZ-800T00A-SEA-ADM1** 가상 머신은 **SEA-DC1** 및 **SEA-ADM1** 의 설치를 호스트합니다.

1. **SEA-ADM1** 을 선택합니다.
1. 다음 자격 증명을 사용하여 로그인합니다.

   - 사용자 이름: **Administrator**
   - 암호: **Pa55w.rd**
   - 도메인: **CONTOSO**

이 랩에서는 사용 가능한 VM 환경과 Azure 구독을 사용합니다. 랩을 시작하기 전에 구독에서 Owner 또는 Contributor 역할이 있는 Azure 구독 및 사용자 계정이 있는지 확인하세요.

## <a name="exercise-1-authoring-arm-templates-for-azure-vm-deployment"></a>연습 1: Azure VM 배포용 ARM 템플릿 권한 부여

### <a name="scenario"></a>시나리오

Azure 기반 작업을 간소화하기 위해 Azure VM에 대한 Windows Server용 자동 배포 및 구성 프로세스를 개발하고 구현하기로 했습니다. 배포는 정보 보안 팀의 요구 사항을 준수하고 Contoso, Ltd.에서 원하는 대상 운영 모델(고가용성 포함)을 준수해야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Azure 구독에 연결하고 클라우드용 Microsoft Defender의 향상된 보안을 사용하도록 설정합니다.
1. Azure Portal을 사용하여 ARM 템플릿 및 매개 변수 파일을 생성합니다.
1. Azure Portal에서 ARM 템플릿과 매개 변수 파일을 다운로드합니다.

#### <a name="task-1-connect-to-your-azure-subscription-and-enable-enhanced-security-of-microsoft-defender-for-cloud"></a>작업 1: Azure 구독에 연결하고 클라우드용 Microsoft Defender의 향상된 보안을 사용하도록 설정

이 작업에서는 Azure 구독에 연결하고 클라우드용 Microsoft Defender의 향상된 보안을 사용하도록 설정합니다.

1. **SEA-ADM1** 에 연결한 다음, 필요하다면 **Pa55w.rd** 암호를 이용해 **CONTOSO\\Administrator** 로 로그인합니다.
1. **SEA-ADM1** 에서 Microsoft Edge를 시작하고 [Azure Portal](https://portal.azure.com)로 이동한 다음, 이 랩에서 사용할 구독에서 소유자 역할이 있는 사용자 계정의 자격 증명을 사용하여 로그인합니다.

>**참고**: Azure 구독에서 클라우드용 Microsoft Defender를 이미 사용하도록 설정했다면 이 작업의 나머지 단계를 건너뛰고 다음 단계로 바로 이동하세요.

1. Azure Portal에서 **클라우드용 Microsoft Defender** 페이지로 이동합니다.
1. **클라우드용 Microsoft Defender \| 시작** 페이지에서 클라우드용 Microsoft Defender의 향상된 보안을 사용하도록 설정하고 자동 클라우드용 Microsoft Defender 에이전트 설치를 활성화합니다.

#### <a name="task-2-generate-an-arm-template-and-parameters-files-by-using-the-azure-portal"></a>작업 2: Azure Portal을 사용하여 ARM 템플릿 및 매개 변수 파일 생성

1. Azure Portal에서 다음 설정을 사용하여 새 Azure VM을 만드는 프로세스를 단계별로 진행하되(다른 모든 VM은 기본값을 유지합니다), 배포하지는 마세요.

   |설정|값|
   |---|---|
   |구독|이 랩에서 사용할 Azure 구독의 이름|
   |Resource group|새 리소스 그룹 **AZ800-L0601-RG** 의 이름|
   |가상 머신 이름|**az800l06-vm0**|
   |지역|Azure 가상 머신을 프로비전할 수 있는 Azure 지역의 이름을 사용합니다.|
   |가용성 옵션|인프라 중복 필요 없음|
   |이미지|**Windows Server 2022 Datacenter: Azure Edition - Gen2**|
   |Azure Spot 인스턴스|예|
   |크기|**Standard_D2s_v3**|
   |사용자 이름|**학생**|
   |암호|**Pa55w.rd1234**|
   |공용 인바운드 포트|없음|
   |기존 Windows Server 라이선스를 사용하시겠습니까?|아니요|
   |OS 디스크 유형|**표준 HDD**|
   |이름|**az800l06-vnet**|
   |주소 범위|**10.60.0.0/20**|
   |서브넷 이름|**subnet0**|
   |서브넷 범위|**10.60.0.0/24**|
   |공용 IP|없음|
   |NIC 네트워크 보안 그룹 추가|없음|
   |가속화된 네트워킹|끄기|
   |기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치하시겠습니까?|아니요|
   |부트 진단|**관리형 스토리지 계정으로 활성화(권장)**|

1. **가상 머신 만들기** 페이지의 **검토 + 만들기** 탭이 표시되면 작업 3을 진행합니다.

   >**참고**: 가상 머신을 만들지 마세요. 이 용도에는 자동 생성된 템플릿을 사용할 것입니다.

#### <a name="task-3-download-the-arm-template-and-parameters-files-from-the-azure-portal"></a>작업 3: Azure Portal에서 ARM 템플릿과 매개 변수 파일 다운로드

1. **가상 머신 만들기 페이지** 의 **검토 + 만들기** 탭에서 자동화용 템플릿을 다운로드하고 랩 VM의 **C:\\Labfiles\\Mod06** 폴더에 복사합니다.
1. Azure Portal에서 **가상 머신 만들기** 페이지를 닫습니다.

## <a name="exercise-2-modifying-arm-templates-to-include-vm-extension-based-configuration"></a>연습 2: VM 확장 기반 구성을 포함하도록 ARM 템플릿 수정

### <a name="scenario"></a>시나리오

자동화된 Azure 리소스 배포에 더해, Azure VM에서 실행하는 Windows Server도 자동으로 구성하려 합니다. 이를 위해 Azure 사용자 지정 스크립트 확장의 사용을 테스트하려 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Azure VM 배포를 위해 ARM 템플릿 및 매개 변수 파일을 검토합니다.
1. 기존 템플릿에 Azure VM 확장 섹션을 추가합니다.

#### <a name="task-1-review-the-arm-template-and-parameters-files-for-azure-vm-deployment"></a>작업 1: Azure VM 배포를 위한 ARM 템플릿 및 매개 변수 파일 검토

1. 다운로드한 보관 파일의 내용을 **C:\\Labfiles\\Mod06** 폴더에 추출합니다.
1. 메모장에서 **template.json** 파일을 열고 내용을 검토합니다. 메모장 창을 계속 열어 두세요.
1. 메모장에서 **C:\\Labfiles\\Mod06\\parameters.json** 파일을 열고 검토한 다음 메모장 창을 닫습니다.

#### <a name="task-2-add-an-azure-vm-extension-section-to-the-existing-template"></a>작업 2: 기존 템플릿에 Azure VM 확장 섹션 추가

1. 랩 VM에서, **template.json** 파일의 내용을 표시하는 메모장 창에서 `    "resources": [` 줄 바로 아래에 다음 코드를 삽입합니다.

   >**참고**: 코드를 한 줄씩 붙여넣는 도구를 사용하는 경우 Intellisense가 유효성 검사 오류를 일으키는 괄호를 추가할 수 있습니다. 먼저 코드를 메모장에 붙여넣은 다음 코드를 JSON 파일에 붙여넣을 수 있습니다.

   ```json
        {
           "type": "Microsoft.Compute/virtualMachines/extensions",
           "name": "[concat(parameters('virtualMachineName'), '/customScriptExtension')]",
           "apiVersion": "2018-06-01",
           "location": "[resourceGroup().location]",
           "dependsOn": [
               "[concat('Microsoft.Compute/virtualMachines/', parameters('virtualMachineName'))]"
           ],
           "properties": {
               "publisher": "Microsoft.Compute",
               "type": "CustomScriptExtension",
               "typeHandlerVersion": "1.7",
               "autoUpgradeMinorVersion": true,
               "settings": {
                   "commandToExecute": "powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)"
              }
           }
        },
   ```
1. 변경 사항을 저장하고 파일을 닫습니다.

## <a name="exercise-3-deploying-azure-vms-running-windows-server-by-using-arm-templates"></a>연습 3: Windows Server에서 실행하는 Azure VM을 ARM 템플릿을 사용하여 배포

### <a name="scenario"></a>시나리오

ARM 템플릿을 구성하면 개념 증명 Azure 구독으로의 배포를 수행하여 템플릿의 기능을 확인해야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. ARM 템플릿을 사용하여 Azure VM을 배포합니다.
1. Azure VM 배포 결과를 검토합니다.

#### <a name="task-1-deploy-an-azure-vm-by-using-an-arm-template"></a>작업 1: ARM 템플릿을 사용하여 Azure VM 배포

1. **SEA-ADM1** 의 Azure Portal에서 **사용자 지정 배포** 페이지로 이동한 다음 **편집기에서 자체 템플릿 빌드** 옵션을 선택합니다.
1. 템플릿 파일 및 매개 변수 파일을 **사용자 지정 배포** 페이지에 로드합니다.
1. 다음 설정을 사용하여 템플릿을 배포하고 다른 설정은 기본값을 변경하지 않습니다.

   |설정|값|
   |---|---|
   |구독|이 랩에서 사용 중인 Azure 구독의 이름|
   |Resource group|**AZ800-L0601-RG**|
   |지역|Azure VM을 프로비전할 수 있는 Azure 지역의 이름|
   |관리자 암호|**Pa55w.rd1234**|

   >**참고**: 배포는 10분 정도 걸릴 수 있습니다.

1. 배포가 성공적으로 완료되었는지 확인합니다.

#### <a name="task-2-review-results-of-the-azure-vm-deployment"></a>작업 2: Azure VM 배포 결과 검토

1. Azure Portal에서 **AZ800-L0601-RG** 리소스 그룹 페이지로 이동한 다음 관련 리소스 목록을, 특히 Azure VM **az800l06-vm0** 을 검토합니다.
1. **az800l06-vm0** Azure VM 페이지로 이동하여 **customScriptExtension** 이 성공적으로 프로비전되었는지 확인합니다.
1. **AZ800-L0601-RG** 리소스 그룹 페이지로 돌아가 배포를 검토하고, 배포에 사용된 **Microsoft.Template** 이 배포에 사용한 템플릿과 일치하는지 확인합니다.

## <a name="exercise-4-configuring-administrative-access-to-azure-vms-running-windows-server"></a>연습 4: Windows Server에서 실행되는 Azure VM에 대한 관리자 액세스 구성

### <a name="scenario"></a>시나리오

Windows Server를 실행하는 Azure VM을 온-프레미스 관리 워크스테이션에서 원격으로 관리하는 기능을 테스트하려 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. 클라우드용 Azure Microsoft Defender의 상태를 확인합니다.
1. Just-In-Time VM 액세스 설정을 검토합니다.

#### <a name="task-1-verify-the-status-of-azure-microsoft-defender-for-cloud"></a>작업 1: 클라우드용 Azure Microsoft Defender의 상태 확인

1. Azure Portal에서 **클라우드용 Microsoft Defender** 페이지로 이동합니다.
1. 클라우드용 Microsoft Defender의 향상된 보안 기능이 사용하도록 설정되었는지 확인합니다.

#### <a name="task-2-review-the-just-in-time-vm-access-settings"></a>작업 2: Just-In-Time VM 액세스 설정을 검토합니다.

1. Azure Portal에서 **클라우드용 Microsoft Defender\| 워크로드 보호** 페이지로 이동하고 **Just-In-Time VM 액세스** 설정을 검토합니다.
1. **Just-In-Time VM 액세스** 페이지에서 **구성됨**, **구성되지 않음** 및 **지원되지 않음** 탭을 검토합니다.

   >**참고**: 새로 배포된 VM이 **지원되지 않음** 탭에 표시되려면 최대 24시간이 걸릴 수 있습니다. 기다리지 말고 다음 연습을 진행하세요.

## <a name="exercise-5-configuring-windows-server-security-in-azure-vms"></a>연습 5: Azure VM에서 Windows Server 보안 구성

### <a name="scenario"></a>시나리오

Windows Server를 실행하는 Azure VM을 온-프레미스 관리 워크스테이션에서 원격으로 관리하는 기능을 테스트하려 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. NSG를 만들고 구성합니다.
1. Azure VM에 대한 인바운드 HTTP 액세스를 구성합니다.
1. Azure VM의 JIT 상태 재평가를 트리거합니다.
1. JIT VM 액세스를 통해 Azure VM에 연결합니다.

#### <a name="task-1-create-and-configure-an-nsg"></a>작업 1: NSG 만들기 및 구성

1. Azure Portal에서 다음 설정을 사용하여 NSG를 만들고, 다른 설정은 기본값을 변경하지 않습니다.

   |설정|값|
   |---|---|
   |구독|이 랩에서 사용 중인 Azure 구독의 이름|
   |Resource group|**AZ800-L0601-RG**|
   |이름|**az800l06-vm0-nsg1**|
   |지역|Azure VM **az800l06-vm0** 을 프로비전한 Azure 지역의 이름|

1. 새로 만든 네트워크 보안 그룹에 다음 설정을 사용하여 인바운드 보안 규칙을 추가하고, 다른 설정은 기본값을 변경하지 않습니다.

   |설정|값|
   |---|---|
   |원본|**임의**|
   |원본 포트 범위|*|
   |대상|**임의**|
   |서비스|**HTTP**|
   |작업|**허용**|
   |우선 순위|**300**|
   |이름|**AllowHTTPInBound**|

#### <a name="task-2-configure-inbound-http-access-to-an-azure-vm"></a>작업 2: Azure VM에 대한 인바운드 HTTP 액세스 구성

1. Azure Portal에서 **az800l06-vm0** Azure VM에 연결된 네트워크 인터페이스 페이지로 이동한 다음 이전 작업에서 만든 네트워크 보안 그룹에 연결합니다.
1. Azure Portal에서 **az800l06-vm0 Azure VM** 에 연결된 네트워크 인터페이스의 IP 구성을 찾은 다음, 다음 설정을 사용하여 새 공용 IP 주소와 연결합니다. 다른 항목은 기본값을 변경하지 않습니다.

   |설정|값|
   |---|---|
   |이름|**az800l06-vm0-pip1**|
   |SKU|**표준**|

1. 랩 VM에서 브라우저 탭을 열고 새로 만든 공용 IP 주소로 이동한 다음, 페이지에 **Hello World from az800l06-vm0** 메시지가 표시되는지 확인합니다.
1. 랩 VM에서 동일한 IP 주소에 대한 원격 데스크톱 연결을 설정하고, 연결 시도가 실패하는지 확인합니다.

   >**참고**: 그 이유는 Azure VM은 현재 TCP 포트 3389를 통해 인터넷에서 액세스할 수 없기 때문입니다. TCP 포트 80을 통해서만 액세스할 수 있습니다.

#### <a name="task-3-trigger-re-evaluation-of-the-jit-status-of-an-azure-vm"></a>작업 3: Azure VM의 JIT 상태 재평가 트리거

>**참고**: 이 작업은 Azure VM의 JIT 상태 재평가를 트리거하는 데 필요합니다. 기본적으로 이 작업은 최대 24시간이 걸릴 수 있습니다.

1. Azure Portal에서 **az800l06-vm0** 페이지로 돌아갑니다.
1. **az800l06-vm0** 페이지에서 **구성** 을 선택합니다. 
1. **az800l06-vm0 \| 구성** 페이지에서 **JIT VM 액세스 사용** 을 선택하고 **Azure Security Center 열기** 링크를 선택합니다.
1. **Just-In-Time VM 액세스** 페이지에서 **az800l06-vm0** Azure VM을 나타내는 항목이 **구성됨** 탭에 표시되는지 확인합니다.

#### <a name="task-4-connect-to-the-azure-vm-via-jit-vm-access"></a>작업 4: JIT VM 액세스를 통해 Azure VM에 연결

1. Azure Portal의 **az800l06-vm0** 페이지에서 JIT VM 액세스를 요청합니다.
1. 요청이 승인되면 대상 Azure VM에 대한 원격 데스크톱 세션을 시작합니다.
1. 자격 증명을 묻는 메시지가 표시되면 다음 값을 지정합니다.
   
   |설정|값|
   |---|---|
   |사용자 이름|**학생**|
   |암호|**Pa55w.rd1234**|

1. 원격 데스크톱을 통해 Azure VM에서 실행하는 운영 체제에 성공적으로 액세스할 수 있는지 확인하고 원격 데스크톱 세션을 닫습니다.

## <a name="exercise-6-deprovisioning-the-azure-environment"></a>연습 6: Azure 환경 프로비전 해제

### <a name="scenario"></a>시나리오

Azure 관련 요금을 최소화하기 위해 이 랩 전체에서 프로비전한 Azure 리소스의 프로비전을 해제하려 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Cloud Shell에서 PowerShell 세션을 시작합니다.
1. 랩에서 프로비전한 모든 Azure 리소스를 식별합니다.

#### <a name="task-1-start-a-powershell-session-in-cloud-shell"></a>작업 1: Cloud Shell에서 PowerShell 세션 시작

1. Azure Portal의 Azure Cloud Shell 창에서 PowerShell 세션을 엽니다.
1. **Cloud Shell** 을 처음 시작하는 경우에는 기본 설정을 적용합니다.

#### <a name="task-2-identify-all-azure-resources-provisioned-in-the-lab"></a>작업 2: 랩에서 프로비전한 모든 Azure 리소스 식별

1. Cloud Shell 창에서 다음 명령을 실행하여 이 랩 전체에서 만든 리소스 그룹을 모두 나열합니다.

   ```powershell
   Get-AzResourceGroup -Name 'AZ800-L06*'
   ```
1. 다음 명령을 실행하여 이 랩 전체에서 만든 리소스 그룹을 모두 삭제합니다.

   ```powershell
   Get-AzResourceGroup -Name 'AZ800-L06*' | Remove-AzResourceGroup -Force -AsJob
   ```

## <a name="results"></a>결과

이 랩을 완료하면 Contoso, Ltd.의 관리 효율성 및 보안 요구 사항을 충족하는 방식으로 Windows Server를 실행하는 Azure VM을 배포하고 구성하게 됩니다.
