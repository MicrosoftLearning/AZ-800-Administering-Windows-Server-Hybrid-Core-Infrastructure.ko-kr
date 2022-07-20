---
lab:
  title: '랩: Azure VM에서 Windows Server 배포 및 구성'
  type: Answer Key
  module: 'Module 6: Deploying and Configuring Azure VMs'
ms.openlocfilehash: 3d6e6a2ef296d3f6dcf02d3137f0aad887616b38
ms.sourcegitcommit: d34dce53481b0263d0ff82913b3f49cb173d5c06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2022
ms.locfileid: "147039451"
---
# <a name="lab-answer-key-deploying-and-configuring-windows-server-on-azure-vms"></a>랩 응답 키: Azure VM에서 Windows Server 배포 및 구성

## <a name="exercise-1-authoring-azure-resource-manager-arm-templates-for-azure-vm-deployment"></a>연습 1: Azure VM 배포를 위한 ARM(Azure Resource Manager) 템플릿 작성

#### <a name="task-1-connect-to-your-azure-subscription-and-enable-enhanced-security-of-microsoft-defender-for-cloud"></a>작업 1: Azure 구독에 연결하고 클라우드용 Microsoft Defender의 향상된 보안을 사용하도록 설정

이 작업에서는 Azure 구독에 연결하고 클라우드용 Microsoft Defender의 향상된 보안을 사용하도록 설정합니다.

1. **SEA-ADM1** 에 연결한 다음, 필요하다면 **Pa55w.rd** 암호를 이용해 **CONTOSO\\Administrator** 로 로그인합니다.
1. **SEA-ADM1** 에서 Microsoft Edge를 시작하고 [Azure Portal](https://portal.azure.com)로 이동한 다음, 이 랩에서 사용할 구독에서 소유자 역할이 있는 사용자 계정의 자격 증명을 사용하여 로그인합니다.

>**참고**: Azure 구독에서 클라우드용 Microsoft Defender를 이미 사용하도록 설정했다면 이 작업의 나머지 단계를 건너뛰고 다음 단계로 바로 이동하세요.

1. Azure Portal에서 도구모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **클라우드용 Microsoft Defender** 를 검색하고 선택합니다.
1. **클라우드용 Microsoft Defender \| 시작** 페이지에서 **업그레이드** 를 선택한 다음, **에이전트 설치** 를 선택합니다.

#### <a name="task-2-generate-an-arm-template-and-parameters-files-by-using-the-azure-portal"></a>작업 2: Azure Portal을 사용하여 ARM 템플릿 및 매개 변수 파일 생성

이 작업에서는 Azure Portal을 사용하여 리소스 그룹을 만들고 리소스 그룹에 디스크를 만듭니다.

1. **SEA-ADM1** 에서, Azure Portal의 도구 모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자를 사용하여 **가상 머신** 을 검색하고 선택합니다. **가상 머신** 페이지에서 **+ 만들기** 를 선택한 다음, **Azure 가상 머신** 을 선택합니다.
1. **가상 머신 만들기** 페이지의 **기본 사항** 탭에서 다음 설정을 지정하고 다른 모든 설정을 그 기본값을 그대로 두지만 배포하지 마세요.

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

1. **다음: 디스크 >** 를 선택한 다음, **가상 머신 만들기** 페이지의 **디스크** 탭에서 다음 설정을 지정하고 다른 모든 설정은 그 기본값을 그대로 둡니다.

   |설정|값|
   |---|---|
   |OS 디스크 유형|**표준 HDD**|

1. **다음: 네트워킹 >** 을 선택하고 **가상 머신 만들기** 페이지의 **네트워킹** 탭에서 **가상 네트워크** 텍스트 상자 뒤에 있는 **새로 만들기** 하이퍼링크를 선택합니다.
1. **가상 네트워크 만들기** 페이지에서 다음 설정을 지정하고 다른 모든 설정은 그 기본값을 그대로 두고 **확인** 을 선택합니다.

   |설정|값|
   |---|---|
   |이름|**az800l06-vnet**|
   |주소 범위|**10.60.0.0/20**|
   |서브넷 이름|**subnet0**|
   |서브넷 범위|**10.60.0.0/24**|

1. 다시 **가상 머신 만들기** 페이지의 **네트워킹** 탭에서 다음 설정을 지정하고 다른 모든 설정은 그 기본값을 그대로 둡니다.

   |설정|값|
   |---|---|
   |공용 IP|없음|
   |NIC 네트워크 보안 그룹 추가|없음|
   |가속화된 네트워킹|끄기|
   |기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치하시겠습니까?|아니요|

1. **다음: 관리 >** 를 선택하고, **가상 머신 만들기** 페이지의 **관리** 탭에서 다음 설정을 지정하고 다른 모든 설정은 그 기본값을 그대로 둡니다.

   |설정|값|
   |---|---|
   |부트 진단|**관리형 스토리지 계정으로 활성화(권장)**|
   
1. **다음: 고급 >** 을 선택하고, **가상 머신 만들기** 페이지의 **고급** 탭에서 사용 가능한 설정을 수정하지 않고 검토한 다음, **검토 + 만들기** 를 선택합니다.

   >**참고**: 가상 머신을 만들지 마세요. 이 용도에는 자동 생성된 템플릿을 사용할 것입니다.

#### <a name="task-3-download-the-arm-template-and-parameters-files-from-the-azure-portal"></a>작업 3: Azure Portal에서 ARM 템플릿과 매개 변수 파일 다운로드

1. Azure Portal의 **가상 머신 만들기** 페이지에서 **자동화를 위한 템플릿 다운로드** 를 선택합니다.
1. **템플릿** 페이지에서 **다운로드** 를 선택합니다.
1. **template.zip** 옆에 있는 줄임표 단추를 선택한 다음, 팝업 메뉴에서 **폴더에 표시** 를 선택합니다. 그러면 **다운로드** 폴더의 내용을 표시하는 파일 탐색기가 자동으로 열립니다.
1. 파일 탐색기에서 **template.zip** 을 **SEA-ADM1** 의 **C:\\Labfiles\\Lab06** 폴더에 복사합니다(필요한 경우 새 폴더를 만듭니다).
1. **템플릿** 페이지에서 **가상 머신 만들기** 페이지로 돌아가, 배포를 완료하지 않고 닫습니다.

## <a name="exercise-2-modifying-arm-templates-to-include-vm-extension-based-configuration"></a>연습 2: VM 확장 기반 구성을 포함하도록 ARM 템플릿 수정

#### <a name="task-1-review-the-arm-template-and-parameters-files-for-azure-vm-deployment"></a>작업 1: Azure VM 배포를 위한 ARM 템플릿 및 매개 변수 파일 검토

1. **SEA-ADM1** 에서 파일 탐색기를 시작한 다음 **C:\\Labfiles\\Lab06** 폴더로 이동합니다.
1. **template.zip** 파일 내용을 동일한 폴더로 추출합니다.
1. 메모장에서 **template.json** 파일을 열고 내용을 검토합니다. 메모장 창을 계속 열어 두세요.
1. 파일 탐색기에서 메모장 **C:\\Labfiles\\Lab06\\parameters.json** 파일을 열고 그 내용을 검토합니다.
1. **parameters.json** 파일을 표시하는 메모장 창을 닫습니다.

#### <a name="task-2-add-an-azure-vm-extension-section-to-the-existing-template"></a>작업 2: 기존 템플릿에 Azure VM 확장 섹션 추가

1. **SEA-ADM1** 서, **template.json** 파일 내용을 표시하는 메모장에서 다음 코드를 `    "resources": [` 라인 바로 뒤에 삽입합니다.
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

#### <a name="task-1-deploy-an-azure-vm-by-using-an-arm-template"></a>작업 1: ARM 템플릿을 사용하여 Azure VM 배포

1. **SEA-ADM1** 에서 Azure Portal이 표시된 브라우저 창으로 전환합니다.
1. Azure Portal의 도구 모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **템플릿 배포** 를 검색하고 선택합니다(사용자 지정 템플릿을 사용하여 배포).
1. **사용자 지정 배포** 페이지에서 **편집기에서 사용자 고유의 템플릿 빌드** 를 선택합니다.
1. **템플릿 편집** 페이지에서 **파일 로드** 를 선택하고 이전 연습에서 편집한 **template.json** 템플릿 파일을 업로드한 다음 **저장** 을 선택합니다.
1. **사용자 지정 배포** 페이지에서 **매개 변수 편집** 을 선택합니다.
1. **매개 변수 편집** 페이지에서 **파일 로드** 를 선택하고 이전 연습에서 검토한 **parameters.json** 매개 변수 파일을 업로드한 다음 **저장** 을 선택합니다.
1. **사용자 지정 배포** 페이지로 돌아가서 다음 설정을 지정하고 나머지 설정은 기본값을 그대로 둡니다.

   |설정|값|
   |---|---|
   |구독|이 랩에서 사용 중인 Azure 구독의 이름|
   |Resource group|**AZ800-L0601-RG**|
   |지역|Azure VM을 프로비전할 수 있는 Azure 지역의 이름|
   |관리자 암호|**Pa55w.rd1234**|

1. **검토 및 만들기** 를 선택한 후 **만들기** 를 선택합니다.

   >**참고**: 배포는 10분 정도 걸릴 수 있습니다.

1. 배포가 성공적으로 완료되었는지 확인합니다.

#### <a name="task-2-review-results-of-the-azure-vm-deployment"></a>작업 2: Azure VM 배포 결과 검토

1. Azure Portal의 도구 모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **리소스 그룹** 을 검색하여 선택합니다.
1. **리소스 그룹** 페이지에서 **AZ800-L0601-RG** 항목을 선택합니다.
1. **AZ800-L0601-RG** 페이지의 **개요** 페이지에서 Azure VM **az800l06-vm0** 을 포함한 리소스 목록을 검토합니다.
1. 리소스 목록 내에서 Azure VM **az800l06-vm0** 항목을 선택합니다. 
1. **az800l06-vm0** 페이지에서 **확장 + 애플리케이션** 을 선택하고 확장 목록에서 **customScriptExtension** 이 성공적으로 프로비저닝되었는지 확인합니다.
1. **AZ800-L0601-RG** 페이지로 돌아가서 **설정** 섹션에서 **배포** 를 선택합니다.
1. **AZ800-L0601-RG \| 배포** 페이지에서 **Microsoft.Template** 링크를 선택합니다.
1. **Microsoft.Template \| 개요** 페이지에서 **템플릿** 을 선택합니다. 이것은 배포에 사용한 것과 동일한 템플릿입니다.

## <a name="exercise-4-configuring-administrative-access-to-azure-vms-running-windows-server"></a>연습 4: Windows Server에서 실행되는 Azure VM에 대한 관리자 액세스 구성

#### <a name="task-1-verify-the-status-of-azure-microsoft-defender-for-cloud"></a>작업 1: 클라우드용 Azure Microsoft Defender의 상태 확인

1. Azure Portal에서 도구모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **클라우드용 Microsoft Defender** 를 검색하고 선택합니다.
1. 클라우드용 Microsoft Defender의 **개요** 페이지에서 왼쪽 세로 메뉴에 있는 **관리** 섹션에서 **환경 설정** 을 선택합니다. 
1. **환경 설정** 페이지에서 Azure 구독을 나타내는 항목을 선택합니다.
1. **설정 \| Defender 계획** 페이지에서 **모든 클라우드용 Microsoft Defender 계획 사용** 타일이 선택되어 있는지 확인하고 왼쪽 세로 메뉴의 **설정** 섹션에서 **자동 프로비저닝** 을 선택합니다.
1. **설정 \| 자동 프로비저닝** 페이지의 확장 목록에서 **Azure VM용 Log Analytics 에이전트** 항목의 오른쪽에 있는 **구성 편집** 링크를 선택합니다.
1. **확장 배포 구성** 에서 **클라우드용 Defender 항목에서 만든 기본 작업 영역으로 Azure VM 연결** 항목을 선택하고 **적용** 을 선택한 다음 다시 **설정\| 자동 프로비저닝** 페이지에서 **저장** 을 선택합니다.

#### <a name="task-2-review-the-just-in-time-vm-access-settings"></a>작업 2: Just-In-Time VM 액세스 설정을 검토합니다.

1. 클라우드용 Microsoft Defender의 **개요** 페이지로 다시 이동한 다음 **Cloud Security** 섹션에서 **워크로드 보호** 를 선택합니다.
1. **클라우드용 Microsoft Defender \| 워크로드 보호** 페이지에서 **Just-in-time VM 액세스** 를 선택합니다.
1. **Just-In-Time VM 액세스** 페이지에서 **구성됨**, **구성되지 않음** 및 **지원되지 않음** 탭을 검토합니다.

   >**참고**: 새로 배포된 VM이 **지원되지 않음** 탭에 표시되려면 최대 24시간이 걸릴 수 있습니다. 기다리지 말고 다음 연습을 진행하세요.

## <a name="exercise-5-configuring-windows-server-security-in-azure-vms"></a>연습 5: Azure VM에서 Windows Server 보안 구성

#### <a name="task-1-create-and-configure-an-nsg"></a>작업 1: NSG 만들기 및 구성

1. Azure Portal의 도구 모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **네트워크 보안 그룹** 을 검색하여 선택합니다.
1. ‘네트워크 보안 그룹’ 페이지에서 **+ 만들기** 를 선택합니다.
1. **네트워크 보안 그룹 만들기** 페이지의 **기본 사항** 탭에서 다음 설정을 지정합니다(다른 설정은 기본값으로 유지).

   |설정|값|
   |---|---|
   |구독|이 랩에서 사용 중인 Azure 구독의 이름|
   |Resource group|**AZ800-L0601-RG**|
   |이름|**az800l06-vm0-nsg1**|
   |지역|Azure VM **az800l06-vm0** 을 프로비전한 Azure 지역의 이름|

1. **네트워크 보안 그룹 만들기** 페이지의 **기본 사항** 탭에서 **검토 + 만들기** 를 선택한 다음 **만들기** 를 선택합니다.
1. Azure Portal에서 **AZ800-L0601-RG** 페이지로 다시 이동한 다음 리소스 목록에서 새로 만든 네트워크 보안 그룹 **az800l06-vm0-nsg1** 을 나타내는 항목을 선택합니다.
1. **az800l06-vm0-nsg1** 페이지에서 기본 인바운드 및 아웃바운드 보안 규칙 목록을 검토한 다음, **설정** 섹션에서 **인바운드 보안 규칙** 을 선택합니다.
1. **az800l06-vm0-nsg1 \| 인바운드 보안 규칙** 페이지에서 **+ 추가** 를 선택합니다.
1. **인바운드 보안 규칙 추가** 페이지에서 다음 설정을 지정하고 다른 모든 설정은 기본값을 그대로 두고 **추가** 를 선택합니다.

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

1. Azure Portal에서 **AZ800-L0601-RG** 페이지로 다시 이동한 다음 리소스 목록에서 Azure VM **az800l06-vm0** 을 나타내는 항목을 선택합니다.
1. **az800l06-vm0** 페이지에서 **네트워킹** 을 선택합니다.
1. **az800l06-vm0 \| 네트워킹** 페이지에서 **az800l06-vm0** 에 연결된 네트워크 인터페이스를 지정하는 링크를 선택합니다.
1. 네트워크 인터페이스 속성을 표시하는 페이지에서 왼쪽 세로 메뉴의 **설정** 섹션에서 **네트워크 보안 그룹** 을 선택합니다. 
1. **네트워크 보안 그룹** 페이지의 드롭다운 목록에서 **az800l06-vm0-nsg1** 을 선택한 다음 **저장** 을 선택합니다.
1. 네트워크 인터페이스의 속성을 표시하는 페이지로 돌아가 **IP 구성** 을 선택한 다음 **ipconfig1** 항목을 선택합니다.
1. **ipconfig1** 페이지의 **공용 IP 주소** 섹션에서 **연결** 을 선택한 다음, **공용 IP 주소** 드롭다운 목록 아래에서 **새로 만들기** 를 선택합니다.
1. **공용 IP 주소 추가** 창에서 다음 설정을 지정한 다음 **확인** 을 선택합니다.

   |설정|값|
   |---|---|
   |이름|**az800l06-vm0-pip1**|
   |SKU|**표준**|

1. **ipconfig1** 페이지로 돌아가 **저장** 을 선택합니다.
1. 네트워크 인터페이스 속성을 표시하는 페이지로 돌아가 **개요** 를 선택합니다. 인터페이스에 할당된 공용 IP 주소의 값을 확인합니다.
1. 다른 브라우저 탭을 열고 해당 IP 주소로 이동한 다음, **az800l06-vm0에서 Hello World** 가 표시된 웹 페이지가 열리는지 확인합니다.
1. 랩 컴퓨터에서 원격 데스크톱 앱을 시작하고 동일한 IP 주소에 연결해 봅니다. 연결이 실패하는지 확인합니다.

   >**참고**: 그 이유는 Azure VM은 현재 TCP 포트 3389를 통해 인터넷에서 액세스할 수 없기 때문입니다. TCP 포트 80을 통해서만 액세스할 수 있습니다.

#### <a name="task-3-trigger-re-evaluation-of-the-jit-status-of-an-azure-vm"></a>작업 3: Azure VM의 JIT 상태 재평가 트리거

>**참고**: 이 작업은 Azure VM의 JIT 상태 재평가를 트리거하는 데 필요합니다. 기본적으로 이 작업은 최대 24시간이 걸릴 수 있습니다.

1. Azure Portal에서 **AZ800-L0601-RG** 페이지로 다시 이동한 다음 리소스 목록에서 Azure VM **az800l06-vm0** 을 나타내는 항목을 선택합니다.
1. **az800l06-vm0** 페이지에서 **구성** 을 선택합니다. 
1. **az800l06-vm0 \| 구성** 페이지에서 **JIT VM 액세스 사용** 을 선택하고 **Azure Security Center 열기** 링크를 선택합니다.
1. **Just-In-Time VM 액세스** 페이지에서 **az800l06-vm0** Azure VM을 나타내는 항목이 **구성됨** 탭에 표시되는지 확인합니다.

#### <a name="task-4-connect-to-the-azure-vm-via-jit-vm-access"></a>작업 4: JIT VM 액세스를 통해 Azure VM에 연결

1. **az800l06-vm0** 페이지로 돌아가 **연결** 을 선택한 다음 드롭다운 목록에서 **RDP** 를 선택합니다. 
1. **az800l06-vm0 \| 연결** 페이지의 **원본 IP** 섹션에서 **내 IP** 를 선택한 다음 **액세스 요청** 을 선택합니다.
1. 요청이 승인되었다는 알림이 표시될 때까지 기다렸다가 **RDP 파일 다운로드** 를 선택하고 메시지에 따라 대상 Azure VM에 연결합니다.
1. 자격 증명을 묻는 메시지가 표시되면 다음 값을 지정한 다음 **확인** 을 선택합니다.

   |설정|값|
   |---|---|
   |사용자 이름|**학생**|
   |암호|**Pa55w.rd1234**|

1. 원격 데스크톱을 통해 Azure VM에서 실행하는 운영 체제에 성공적으로 액세스할 수 있는지 확인하고 원격 데스크톱 세션을 닫습니다.

## <a name="exercise-6-deprovisioning-the-azure-environment"></a>연습 6: Azure 환경 프로비전 해제

#### <a name="task-1-start-a-powershell-session-in-cloud-shell"></a>작업 1: Cloud Shell에서 PowerShell 세션 시작

1. **SEA-ADM1** 서, Azure Portal이 표시된 Microsoft Edge 창에서 Cloud Shell 아이콘을 선택하여 Azure Cloud Shell 창을 엽니다.
1. **Bash** 와 **PowerShell** 중에서 선택하라는 메시지가 표시되면 **PowerShell** 을 선택합니다.

   >**참고:** Cloud Shell을 처음 시작하는 경우 **탑재된 스토리지가 없음** 메시지가 표시되면 이 랩에서 사용 중인 구독을 선택한 후 **스토리지 만들기** 를 선택합니다.

#### <a name="task-2-identify-all-azure-resources-provisioned-in-the-lab"></a>작업 2: 랩에서 프로비전한 모든 Azure 리소스 식별

1. Cloud Shell 창에서 다음 명령을 실행하여 이 랩 전체에서 만든 리소스 그룹을 모두 나열합니다.

   ```powershell
   Get-AzResourceGroup -Name 'AZ800-L06*'
   ```
1. 다음 명령을 실행하여 이 랩 전체에서 만든 리소스 그룹을 모두 삭제합니다.

   ```powershell
   Get-AzResourceGroup -Name 'AZ800-L06*' | Remove-AzResourceGroup -Force -AsJob
   ```
   >**참고**: 명령은 *-AsJob* 매개 변수에 의해 결정된 대로 비동기적으로 실행됩니다. 따라서 동일한 PowerShell 세션에서 즉시 다른 PowerShell 명령을 실행할 수 있지만, 리소스 그룹이 실제로 제거되려면 몇 분 정도 걸립니다.
