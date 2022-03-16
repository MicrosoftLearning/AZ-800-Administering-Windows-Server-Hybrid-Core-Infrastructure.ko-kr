---
lab:
  title: '랩: Azure 파일 동기화 구현'
  module: 'Module 10: Implementing a hybrid file server infrastructure'
ms.openlocfilehash: d29195536db0447200bba7faa49e87b586157883
ms.sourcegitcommit: bd43c7961e93ef200b92fb1d6f09d9ad153dd082
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2022
ms.locfileid: "137906996"
---
# <a name="lab-implementing-azure-file-sync"></a>랩: Azure 파일 동기화 구현

## <a name="scenario"></a>시나리오

Contoso의 런던 본사와 시애틀 지점 간의 DFS(분산 파일 시스템) 복제와 관련된 문제를 해결하기 위해 두 온-프레미스 파일 공유 간의 대체 복제 메커니즘으로 Azure 파일 동기화를 테스트하기로 결정합니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음을 수행할 수 있습니다.

- 온-프레미스 환경에서 DFS 복제 구현
- 동기화 그룹 생성 및 구성
- DFS 복제를 Azure 파일 동기화 기반 복제로 교체
- 복제의 유효성 검사 및 클라우드 계층화 활성화
- 복제 충돌 문제 해결

## <a name="estimated-time-60-minutes"></a>예상 시간: 60분

## <a name="lab-setup"></a>랩 설정

가상 머신: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, **AZ-800T00A-SEA-SVR2**, **AZ-800T00A-ADM1** 이 실행 중이어야 합니다. 

> **참고**: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, **AZ-800T00A-SEA-SVR2**, **AZ-800T00A-SEA-ADM1** 가상 머신은 각각 **SEA-DC1**, **SEA-SVR1**, **SEA-SVR2**, **SEA-ADM1** 의 설치를 호스트합니다.

1. **SEA-ADM1** 을 선택합니다.
1. 다음 자격 증명을 사용하여 로그인합니다.

   - 사용자 이름: **Administrator**
   - 암호: **Pa55w.rd**
   - 도메인: **CONTOSO**

이 랩에서는 사용 가능한 VM 환경과 Azure 구독을 사용합니다. 랩을 시작하기 전에 구독에서 Owner 또는 Contributor 역할이 있는 Azure 구독 및 사용자 계정이 있는지 확인하세요.

## <a name="exercise-1-implementing-dfs-replication-in-your-on-premises-environment"></a>연습 1: 온-프레미스 환경에서 DFS 복제 구현

### <a name="scenario"></a>시나리오

연습 시나리오: 온-프레미스 DFS 복제 마이그레이션 테스트를 시작하기 전에 먼저 **SEA-SVR1** 및 **SEA-SVR2** 의 개념 증명 환경에서 DFS 복제를 구현해야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. DFS 배포
1. DFS 배포 테스트

#### <a name="task-1-deploy-dfs"></a>작업 1: DFS 배포

1. **SEA-ADM1** 에서 관리자 권한으로 Windows PowerShell을 시작하고 다음 명령을 실행하여 DFS(분산 파일 시스템) 관리 도구를 설치합니다.

   ```powershell
   Install-WindowsFeature -Name RSAT-DFS-Mgmt-Con -IncludeManagementTools
   ```
1. **SEA-ADM1** 에서 파일 탐색기를 열고 **C:\\LabfilesLab10\\** 폴더로 이동한 다음 새 **Windows PowerShell ISE** 창의 스크립트 창에서 **L10-DeployDFS.ps1** 을 엽니다.
1. 스크립트 창에서 스크립트를 검토한 다음 실행하여 샘플 DFS 네임스페이스 및 DFS 복제 그룹을 만듭니다.

#### <a name="task-2-test-dfs-deployment"></a>작업 2: DFS 배포 테스트

1. **SEA-ADM1** 에서 **DFS 관리** 콘솔을 시작하고 이전 작업에서 만든 **\\\\Contoso.com\\Root\\** 네임스페이스와 **Branch1** 복제 그룹 모두에 추가합니다. 
1. **\\\\Contoso.com\\Root\\Data** 폴더의 **SEA-SVR1** 및 **SEA-SVR2** 에 대상이 있는지 확인합니다. 대상으로 구성된 폴더를 확인합니다.
1. **Branch1** 복제 그룹에 **SEA-SVR1** 및 **SEA-SVR2** 의 두 멤버가 있는지 확인합니다. 각 서버에 복제된 폴더를 확인합니다.
1. 파일 탐색기 인스턴스를 두 개 엽니다. 첫 번째 인스턴스에서 **\\\\SEA-SVR1\\Data** 에 연결한 다음 두 번째 인스턴스에서 **\\\\SEA-SVR2\\Data** 에 연결합니다.
1. **\\\\SEA-SVR1\\Data** 에서 사용자 이름으로 새 파일을 만든 다음 몇 초 후 파일이 **\\\\SEA-SVR2\\Data** 에 복제되는지 확인합니다. 이렇게 하면 DFS 복제가 작동하는 것이 확인됩니다.

   >**참고:** 파일이 복제되고 두 파일 탐색기 창이 모두 동일한 콘텐츠를 기록할 때까지 기다리세요.

### <a name="results"></a>결과

이 연습을 완료하면 작동하는 DFS 인프라가 만들어집니다. 여기에는 DFS 복제가 포함되며, **SEA-SVR1** 및 **SEA-SVR2** 사이의 콘텐츠를 복제합니다.

## <a name="exercise-2-creating-and-configuring-a-sync-group"></a>연습 2: 동기화 그룹 만들기 및 구성

### <a name="scenario"></a>시나리오

DFS 복제 환경을 파일 동기화로 마이그레이션할 준비를 하려면 먼저 파일 동기화 그룹을 만들고 구성해야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. Azure 파일 공유를 만듭니다.
1. Azure 파일 공유를 사용합니다.
1. 스토리지 동기화 서비스 및 파일 동기화 그룹 배포

#### <a name="task-1-create-an-azure-file-share"></a>작업 1: Azure 파일 공유 만들기

1. **SEA-ADM1** 에서 Microsoft Edge를 시작하고 Azure Portal로 이동한 다음 Azure 자격 증명으로 인증합니다.
1. Azure Portal에서 **AZ800-L1001-RG** 라는 리소스 그룹에 **LRS(로컬 중복 스토리지)** 가 있는 Azure Storage 계정을 만듭니다.

   >**참고:** 이 랩에서는 동일한 지역을 사용하여 모든 Azure 리소스를 배포합니다.

1. 스토리지 계정에서 **share1** 이라는 파일 공유를 만듭니다.

#### <a name="task-2-use-an-azure-file-share"></a>작업 2: Azure 파일 공유 사용

1. **SEA-ADM1** 에서 **C:\\Labfiles\\Lab10\\File1.txt** 파일을 **share1** 에 업로드합니다.
1. Azure Portal에서 **share1** 의 스냅샷을 만듭니다.
1. **SEA-ADM1** 에서 Azure Portal이 제공하는 연결 스크립트를 사용하여 **share1** 을 드라이브 **Z** 에 탑재합니다.
1. 파일 탐색기에서 탑재된 드라이브의 **File1.txt** 라는 파일을 열고 이름을 입력한 다음 파일을 저장합니다.
1. 파일 탐색기에서 **이전 버전** 을 사용하여 이전 버전의 **File1.txt** 복원합니다.
1. **File1.txt** 를 열고 이름을 포함하지 않는지 확인합니다.

#### <a name="task-3-deploy-storage-sync-service-and-a-file-sync-group"></a>작업 3: 스토리지 동기화 서비스 및 파일 동기화 그룹 배포

1. **SEA-ADM1** 에서 Azure Portal을 사용하여 **FileSync1** 이라는 Azure 파일 동기화 리소스를 만듭니다. 스토리지 계정을 배포할 때 사용한 것과 동일한 지역 및 리소스 그룹을 사용합니다.

   >**참고:** 파일 동기화를 배포하면 Storage Sync Service 리소스가 만들어집니다.

1. **FileSync1** Storage Sync Service에서 **Sync1** 이라는 동기화 그룹을 만듭니다. 이전에 만든 스토리지 계정을 사용하고 **Sync1** 을 만들 때 **share1** 을 Azure 파일 공유로 사용합니다.
1. 현재 **FileSync1** 에 등록된 서버가 없는지 확인합니다.

### <a name="results"></a>결과

이 연습을 완료하고 나면 파일 동기화 그룹을 만들게 됩니다. 또한 Azure 파일 공유 콘텐츠를 검사할 수 있도록 **SEA-ADM1** 에 매핑된 클라우드 엔드포인트를 만들었습니다.

## <a name="exercise-3-replacing-dfs-replication-with-file-sync-based-replication"></a>연습3: DFS 복제를 파일 동기화 기반 복제로 교체

### <a name="scenario"></a>시나리오

이제 필요한 모든 구성 요소가 마련되었으므로 DFS 복제를 파일 동기화 기반 복제로 바꿔야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. **SEA-SVR1** 을 서버 엔드포인트로 추가
1. **SEA-SVR2** 를 파일 동기화에 등록
1. DFS 복제를 제거하고 **SEA-SVR2** 를 서버 엔드포인트로 추가

#### <a name="task-1-add-sea-svr1-as-a-server-endpoint"></a>작업 1: SEA-SVR1을 서버 엔드포인트로 추가

1. **SEA-ADM1** 의 Azure Portal에서 Windows Server 2022(**StorageSyncAgent_WS2022.msi**)의 파일 동기화 에이전트를 다운로드하고 **C:\\\\Labfiles\\Lab10** 폴더에 저장합니다.
1. **SEA-ADM1** 의 파일 탐색기에서 **C:\\Labfiles\\Lab10** 폴더로 이동한 다음 Windows PowerShell ISE 창의 스크립트 창에서 **Install-FileSyncServerCore.ps1** 을 엽니다.
1. **Windows PowerShell ISE** 스크립트 창에서 스크립트를 검토하고 실행하여 **SEA-SVR1** 에 파일 동기화 에이전트를 설치합니다.
1. 메시지가 표시되면 Azure 구독에 대해 인증합니다. 
1. Azure Portal에서 **FileSync1** Storage Sync Service에서 등록된 서버를 새로 고친 다음 **SEA-SVR1.Contoso.com** 이 이제 등록되어 있음을 보여줍니다.
1. 파일 탐색기에서 **\\\\SEA-SVR1\\Data** 를 연 다음 폴더에 **File1.txt** 가 포함되어 있지 않음을 보여줍니다.
1. Azure Portal을 사용하여 **SEA-SVR2.Contoso.com** 의 **S:\\Data** 를 **Sync1** 에 대한 서버 엔드포인트로 추가합니다.
1. 파일 탐색기를 사용하여 **SEA-SVR1Data** **에서\\\\\\\\File1.txt** 를 사용할 수 있는지 확인합니다.

   >**참고:** **File1.txt** 를 **File1.txtAzure** 파일 공유에 업로드했으며, 여기에서 파일 동기화로 **SEA-SVR2** 에 동기화되었습니다.

#### <a name="task-2-register-sea-svr2-with-file-sync"></a>작업 2: SEA-SVR2를 파일 동기화에 등록

1. **SEA-ADM1** 에 있는 **Install-FileSyncServerCore.ps1** 스크립트가 표시되는 Windows PowerShell ISE 창에서 `SEA-SVR1`을 `SEA-SVR2`로 변경한 다음 변경사항을 저장합니다.
1. **C:\\Labfiles\\Lab10\\Install-FileSyncServerCore.ps1** 을 실행하여 파일 동기화 에이전트를 **SEA-SVR2** 에 설치합니다.
1. 메시지가 표시되면 Azure 구독에 대해 인증합니다. 
1. Azure Portal을 사용하여 **SEA-SVR2.Contoso.com** 및 **SEA-SVR1.Contoso.com** 이 **FileSync1** Storage Sync Service에 등록되었는지 확인합니다.

#### <a name="task-3-remove-dfs-replication-and-add-sea-svr2-as-a-server-endpoint"></a>작업 3: DFS 복제를 제거하고 SEA-SVR2를 서버 엔드포인트로 추가

1. **SEA-ADM1** 에서 **DFS 관리** 를 사용하여 **Branch1** 복제 그룹을 삭제합니다.
1. Azure Portal을 사용하여 **SEA-SVR2.Contoso.com** 의 **S:\\Data** 를 **Sync1** 에 대한 서버 엔드포인트로 추가합니다.

### <a name="results"></a>결과

이 연습을 완료하고 나면 DFS 복제를 파일 동기화로 바꾸게 됩니다.

## <a name="exercise-4-verifying-replication-and-enabling-cloud-tiering"></a>연습 4: 복제 확인 및 클라우드 계층화 사용

### <a name="scenario"></a>시나리오

연습 시나리오: 이제 DFS 복제를 파일 동기화로 성공적으로 대체했는지 확인해야 하며, 이를 확인한 후에는 클라우드 계층을 활성화해야 합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. 파일 동기화 확인
1. 클라우드 계층화 활성화

#### <a name="task-1-verify-file-sync"></a>작업 1: 파일 동기화 확인

1. **SEA-ADM1** 에서 파일 탐색기의 두 인스턴스를 사용하여 **\\\\SEA-SVR1\\Data** 및 **\\\\SEA-SVR2\\Data** 의 콘텐츠를 표시합니다.
1. **\\\\SEA-SVR1\\Data** 폴더에 임의의 이름으로 파일을 만듭니다.
1. 잠시 후 **\\\\SEA-SVR2\\Data** 폴더에 이름이 같은 파일이 나타나는지 확인합니다.

   >**참고** 이전 연습에서 DFS 복제를 제거했습니다. 즉, 파일 동기화가 새로 만든 파일을 복제했습니다.

#### <a name="task-2-enable-cloud-tiering"></a>작업 2: 클라우드 계층화 사용

1. **SEA-ADM1** 에서 Azure Portal을 사용하여 **FileSync1** Storage Sync Service의 **Sync1** 동기화 그룹으로 이동합니다.
1. Azure Portal에서 **Sync1** 의 **SEA-SVR1.Contoso.com** 엔드포인트에 대해 클라우드 계층을 활성화합니다. **사용 가능한 디스크 공간** 정책을 **80** 퍼센트로 설정하고 지난 **7** 일 동안 액세스한 파일을 캐시하도록 **날짜 정책** 을 설정합니다.
1. **\\\\SEA-SVR1\\Data** 폴더에 연결된 파일 탐색기 인스턴스에 있는 결과 창에서 마우스 오른쪽 버튼을 클릭하거나 **제목** 열의 바로 가기 메뉴에 액세스하여 **특성** 열을 추가합니다. 예를 들면, **Name** 열에서 **더보기** 를 선택한 다음 **특성** 을 선택합니다.

   >**참고:** 시간이 지나면 **SEA-SVR2** 의 파일이 자동으로 계층화됩니다. PowerShell을 사용하여 이 프로세스를 트리거합니다.

1. **SEA-ADM1** 에 있는 **Windows PowerShell ISE** 의 콘솔 창에서 다음 명령을 실행하여 즉시 계층화를 트리거합니다.

   ```powershell
   Enter-PSSession -computername SEA-SVR2
   fsutil file createnew S:\Data\report1.docx 254321098
   fsutil file createnew S:\Data\report2.docx 254321098
   fsutil file createnew S:\Data\report3.docx 254321098
   fsutil file createnew S:\Data\report4.docx 254321098
   Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
   Invoke-StorageSyncCloudTiering -Path S:\Data 
   ```
1. **SEA-ADM1** 에서 파일 탐색기로 전환한 다음 **\\\\SEA-SVR2\\Data** 공유에서 속성이 **L**, **M** 및 **O**(계층화가 이루어졌음을 나타냄)인 파일을 식별합니다.

### <a name="results"></a>결과

이 연습을 완료하고 나면 작동하는 파일 동기화 복제 및 구성된 클라우드 계층화가 만들어집니다.

## <a name="exercise-5-troubleshooting-replication-issues"></a>연습 5: 복제 문제 해결

### <a name="scenario"></a>시나리오

연습 시나리오: Contoso는 DFS 복제 구현에 크게 의존합니다. 복제 충돌을 포함한 모든 복제 문제를 신속하게 식별하고 해결할 수 있어야 합니다. 이를 위해 개념 증명 환경에서 가장 일반적인 복제 문제를 시뮬레이션하고 해결 방법을 테스트합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. 파일 동기화 복제 모니터링
1. 복제 충돌 해결 테스트

#### <a name="task-1-monitor-file-sync-replication"></a>작업 1: 파일 동기화 복제 모니터링

1. **SEA-ADM1** 에서 파일 탐색기를 사용하여 **C:\\Windows\\INF** 폴더를 **\\\\SEA-SVR1\Data\\** 에 복사합니다. 폴더가 클라우드 엔드포인트와 동기화되어 동기화 트래픽이 발생합니다.
1. Azure Portal에서 **FileSync1** Storage Sync Service의 **Sync1** 동기화 그룹으로 이동합니다.
1. **서버 엔드포인트** 섹션에서 두 엔드포인트의 **상태** 를 확인합니다.
1. **SEA-SVR1.Contoso.com** 엔드포인트를 선택하고 서버 엔드포인트 속성 창에서 **동기화 작업** 을 검토합니다.
1. **동기화된 파일 수** 그래프를 선택한 다음 필터를 사용하여 그래프를 사용자 지정하는 방법을 탐색합니다.
1. **INF** 폴더가 **Z** 드라이브에 동기화되는지 확인합니다.
1. Azure Portal에서 **INF** 동기화 트래픽이 **동기화된 파일 수** 및 **동기화된 바이트 수** 그래프에 반영되는지 확인합니다. **INF** 폴더에는 800개 이상의 파일이 있으며 크기가 40메가바이트(MB)를 넘습니다.

   >**참고:** 업데이트된 통계를 보려면 Azure Portal이 표시되는 페이지를 새로 고쳐야 할 수 있습니다.

#### <a name="task-2-test-replication-conflict-resolution"></a>작업 2: 복제 충돌 해결 테스트

1. **SEA-ADM1** 에서 **\\\\SEA-SVR1\Data\\** 및 **\\\\SEA-SVR2\Data\\** 의 내용이 나란히 표시되도록 파일 탐색기 창을 배치합니다.
1. **\\\\SEA-SVR1\Data\\** 의 내용이 표시되는 파일 탐색기 창에서 **Demo.txt** 라는 파일을 만듭니다. 
1. **\\\\SEA-SVR2\Data\\** 의 내용이 표시되는 파일 탐색기 창에서 **Demo.txt** 라는 파일을 만듭니다. 
1. 첫 번째 **Demo.txt** 파일에 임의의 텍스트를 추가하고 변경 내용을 저장합니다.
1. 그런 다음 바로 위 단계에서 사용한 것과는 다른 임의의 텍스트를 두 번째 **Demo.txt** 파일에 추가하고 변경 내용을 저장합니다.

   >**참고:** 최대한 빨리 두 번째 파일에 변경 사항을 저장해야 합니다. 이름이 같지만 내용이 다른 파일을 만들어 의도적으로 동기화 충돌을 트리거하는 것입니다.

1. 각 파일 탐색기 창에서 내용을 확인하고 **Demo.txt** 파일 외에도 **Demo-SEA-SVR2.txt** 파일(및 잠재적으로 **Demo-Cloud.txt**)도 포함하고 있는지 확인합니다. 

   >**참고:** 파일 동기화가 동기화 충돌을 감지하여 충돌을 일으킨 파일에 엔드포인트 이름(**SEA-SVR2**) 또는 **Cloud** 를 나타내는 접미사를 추가했기 때문입니다.

### <a name="results"></a>결과

이 연습을 완료하고 나면 파일 동기화 복제를 모니터링하고 복제 충돌을 해결하게 됩니다.

## <a name="exercise-6-cleaning-up-the-azure-subscription"></a>연습 6: Azure 구독 정리

연습 시나리오: Azure 관련 요금을 최소화하려면 Azure 구독을 정리합니다.

#### <a name="task-1-delete-the-azure-resources-that-were-created-in-the-lab"></a>작업 1: 랩에서 만든 Azure 리소스 삭제

1. **SEA-ADM1** 에서 Azure Portal을 사용하여 **FileSync1 Storage Sync Service** 페이지로 이동합니다.
1. 등록된 서버인 **SEA-SVR1.Contoso.com** 및 **SEA-SVR2.Contoso.com** 를 제거합니다.
1. **Sync1** 동기화 그룹에서 **share1** 클라우드 엔드포인트를 삭제합니다.
1. **Sync1** 동기화 그룹을 삭제합니다.
1. **FileSync1** Storage Sync Service와 랩에서 만든 Azure 스토리지 계정을 삭제합니다.
1. **AZ800-L1001-RG** 리소스 그룹을 삭제합니다.

### <a name="results"></a>결과

이 연습을 완료하고 나면 랩에서 만든 Azure 리소스가 정리됩니다.
