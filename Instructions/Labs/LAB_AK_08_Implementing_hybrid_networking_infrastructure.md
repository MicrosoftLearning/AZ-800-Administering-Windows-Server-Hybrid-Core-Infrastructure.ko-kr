---
lab:
  title: '랩: Windows Server IaaS VM 네트워킹 구현'
  type: Answer Key
  module: 'Module 8: Implementing Windows Server IaaS VM networking'
ms.openlocfilehash: b9ae6e391351ac7614b5129914173c78592309b0
ms.sourcegitcommit: bd43c7961e93ef200b92fb1d6f09d9ad153dd082
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2022
ms.locfileid: "137906983"
---
# <a name="lab-implementing-hybrid-networking-infrastructure"></a>랩: 하이브리드 네트워킹 인프라 구현

### <a name="exercise-1-implement-virtual-network-routing-in-azure"></a>연습 1: Azure에서 가상 네트워크 라우팅 구현

#### <a name="task-1-provision-lab-infrastructure-resources"></a>작업 1: 랩 인프라 리소스 프로비전

1. **SEA-ADM1** 에 연결한 다음, 필요하다면 **Pa55w.rd** 암호를 이용해 **CONTOSO\\Administrator** 로 로그인합니다.
1. **SEA-ADM1** 에서 Microsoft Edge를 시작하고 **[Azure Portal](https://portal.azure.com)** 로 이동한 다음, 이 랩에서 사용할 구독에서 소유자 역할이 있는 사용자 계정의 자격 증명을 사용하여 로그인합니다.
1. Azure Portal에서 검색 텍스트 상자 옆의 도구 모음 아이콘을 선택하여 Cloud Shell 창을 엽니다.
1. **Bash** 와 **PowerShell** 중에서 선택하라는 메시지가 표시되면 **PowerShell** 을 선택합니다.

   >**참고**: Cloud Shell을 처음 시작하는 경우 **탑재된 스토리지가 없음** 메시지가 표시되면 이 랩에서 사용 중인 구독을 선택한 후 **스토리지 만들기** 를 선택합니다.

1. Cloud Shell 창의 도구 모음에서 **일 업로드/다운로드** 아이콘을 선택하고, 드롭다운 메뉴에서 **로드** 를 선택하고, 파일 **C:\\Labfiles\\Lab08\\L08-rg_template.json** 및 **C:\\Labfiles\\Lab08\\L08-rg_template.parameters.json** 을 Cloud Shell 홈 디렉터리에 업로드합니다.
1. Cloud Shell 창에서 다음 명령을 실행하여 랩 환경을 호스팅할 첫 번째 리소스 그룹을 만듭니다(`<Azure_region>` 자리 표시자를 배포에 사용할 Azure 지역의 이름으로 바꿉니다).

   >**참고**: **(Get-AzLocation).Location** 명령을 실행하여 사용 가능한 Azure 지역의 이름을 나열할 수 있습니다.

    ```powershell 
    $location = '<Azure_region>'
    $rgName = 'AZ800-L0801-RG'
    New-AzResourceGroup -Name $rgName -Location $location
    ```
1. Cloud Shell 창에서 다음 명령을 실행하고, 업로드한 템플릿 및 매개 변수 파일을 사용하여 가상 네트워크 3개와 Azure VM 4개를 만듭니다.

   ```powershell
   New-AzResourceGroupDeployment `
      -ResourceGroupName $rgName `
      -TemplateFile $HOME/L08-rg_template.json `
      -TemplateParameterFile $HOME/L08-rg_template.parameters.json
   ```

    >**참고**: 배포가 완료될 때까지 기다린 후 다음 작업을 진행하세요. 이 작업은 3분 정도 걸립니다.

1. Cloud Shell 창에서 다음 명령을 실행하여 이전 단계에서 배포된 Azure VM에 Network Watcher 확장을 설치합니다.

   ```powershell
   $rgName = 'AZ800-L0801-RG'
   $location = (Get-AzResourceGroup -ResourceGroupName $rgName).location
   $vmNames = (Get-AzVM -ResourceGroupName $rgName).Name

   foreach ($vmName in $vmNames) {
     Set-AzVMExtension `
     -ResourceGroupName $rgName `
     -Location $location `
     -VMName $vmName `
     -Name 'networkWatcherAgent' `
     -Publisher 'Microsoft.Azure.NetworkWatcher' `
     -Type 'NetworkWatcherAgentWindows' `
     -TypeHandlerVersion '1.4'
   }
   ```

    >**참고**: 배포가 완료될 때까지 기다리지 말고 다음 작업을 진행하세요. Network Watcher 확장을 설치하는 데 약 5분이 걸립니다.

#### <a name="task-2-configure-the-hub-and-spoke-network-topology"></a>작업 2: 허브 및 스포크 네트워크 토폴로지 구성

1. **SEA-ADM1** 에서, Azure Portal을 표시하는 Microsoft Edge 창에서 탭을 하나 더 열고 **[Azure Portal](https://portal.azure.com)** 로 이동합니다.
1. Azure Portal의 도구 모음의 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **가상 네트워크** 를 검색하여 선택합니다.
1. 가상 네트워크 목록에서 **az800l08-vnet1** 을 선택합니다.
1. **az800l08-vnet1** 가상 네트워크 페이지의 **설정** 섹션에서 **피어링** 을 선택한 다음 **+ 추가** 를 선택합니다.
1. 다음 설정을 지정하고(다른 설정은 기본값으로 유지) **추가** 를 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | 이 가상 네트워크: 피어링 링크 이름 | **az800l08-vnet0_to_az800l08-vnet1** |
    | 원격 가상 네트워크로의 트래픽 | **허용(기본값)** |
    | 원격 가상 네트워크에서 전달된 트래픽 | **허용(기본값)** |
    | 가상 네트워크 게이트웨이 또는 Route Server | **없음(기본값)** |
    | 원격 가상 네트워크: 피어링 링크 이름 | **az800l08-vnet1_to_az800l08-vnet0** |
    | 가상 네트워크 배포 모델 | **리소스 관리자** |
    | 원격 가상 네트워크: 가상 네트워크 | **az800l08-vnet1** |
    | 원격 가상 네트워크로의 트래픽 | **허용(기본값)** |
    | 원격 가상 네트워크에서 전달된 트래픽 | **허용(기본값)** |
    | 가상 네트워크 게이트웨이 | **없음(기본값)** |

    >**참고**: 작업이 완료될 때까지 기다립니다.

    >**참고**: 이 단계에서 두 피어링을 설정합니다(하나는 **az800l08-vnet0** 에서 **az800l08-vnet1** 로, 다른 하나는 **az800l08-vnet1** 에서 **az800l08-vnet0** 로).

    >**참고**: 이 랩의 뒷부분에서 구현할 스포크 가상 네트워크 간의 라우팅을 용이하게 하려면 **전달된 트래픽** 을 사용하도록 설정해야 합니다.

1. **az800l08-vnet0** 가상 네트워크 페이지의 **설정** 섹션에서 **피어링** 을 선택한 다음 **+ 추가** 를 선택합니다.
1. 다음 설정을 지정하고(다른 설정은 기본값으로 유지) **추가** 를 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | 이 가상 네트워크: 피어링 링크 이름 | **az800l08-vnet0_to_az800l08-vnet2** |
    | 원격 가상 네트워크로의 트래픽 | **허용(기본값)** |
    | 원격 가상 네트워크에서 전달된 트래픽 | **허용(기본값)** |
    | 가상 네트워크 게이트웨이 | **없음(기본값)** |
    | 원격 가상 네트워크: 피어링 링크 이름 | **az800l08-vnet2_to_az800l08-vnet0** |
    | 가상 네트워크 배포 모델 | **리소스 관리자** |
    | 원격 가상 네트워크: 가상 네트워크 | **az800l08-vnet2** |
    | 원격 가상 네트워크로의 트래픽 | **허용(기본값)** |
    | 원격 가상 네트워크에서 전달된 트래픽 | **허용(기본값)** |
    | 가상 네트워크 게이트웨이 | **없음(기본값)** |

    >**참고**: 이 단계에서 두 피어링을 설정합니다(하나는 **az800l08-vnet0** 에서 **az800l08-vnet2** 로, 다른 하나는 **az800l08-vnet2** 에서 **az800l08-vnet0** 로). 이제 허브 및 스포크 토폴로지 설정이 완료됩니다(**az800l08-vnet0** 가상 네트워크는 허브 역할을 하며 **az800l08-vnet1** 및 **az800l08-vnet2** 는 그 스포크임).

#### <a name="task-3-test-transitivity-of-virtual-network-peering"></a>작업 3: 가상 네트워크 피어링의 전이성 테스트

>**참고**: 이 작업을 시작하기 전에 이 연습의 첫 번째 작업에서 호출한 스크립트가 성공적으로 완료되었는지 확인합니다.

1. Azure Portal에서 **Network Watcher** 를 검색하고 선택합니다.
1. **Network Watcher** 페이지에서 **연결 문제 해결** 로 이동합니다.
1. **Network Watcher - 연결 문제 해결** 페이지에서 다음 설정을 사용하여 확인을 시작합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 |
    | --- | --- |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | Resource group | **AZ800-L0801-RG** |
    | 소스 형식 | **가상 머신** |
    | 가상 머신 | **az800l08-vm0** |
    | 대상 | **수동으로 지정** |
    | URI, FQDN 또는 IPv4 | **10.81.0.4** |
    | 프로토콜 | **TCP** |
    | 대상 포트 | **3389** |

    > **참고**: **10.81.0.4** 는 **az800l08-vm1** 의 개인 IP 주소를 나타냅니다. 원격 데스크톱은 기본적으로 Azure 가상 머신에서 사용하도록 설정되고 가상 네트워크 내 및 가상 네트워크 간 액세스가 가능하므로 이 테스트에서는 **TCP** 포트 **3389** 를 사용합니다.

1. **확인** 을 선택하고 연결 확인 결과가 반환될 때까지 기다립니다. 상태가 **연결할 수 있음** 인지 확인합니다. 네트워크 경로를 검토하고 VM 간에 중간 홉 없이 직접 연결되었는지 확인합니다.

    > **참고**: 그 이유는 허브 가상 네트워크가 첫 번째 스포크 가상 네트워크와 직접 피어링되기 때문입니다.

1. **Network Watcher - 연결 문제 해결** 페이지에서 다음 설정을 사용하여 확인을 시작합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 |
    | --- | --- |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | Resource group | **AZ800-L0801-RG** |
    | 소스 형식 | **가상 머신** |
    | 가상 머신 | **az800l08-vm0** |
    | 대상 | **수동으로 지정** |
    | URI, FQDN 또는 IPv4 | **10.82.0.4** |
    | 프로토콜 | **TCP** |
    | 대상 포트 | **3389** |

    > **참고**: **10.82.0.4** 는 **az800l08-vm2** 의 개인 IP 주소를 나타냅니다. 

1. **확인** 을 선택하고 연결 확인 결과가 반환될 때까지 기다립니다. 상태가 **연결할 수 있음** 인지 확인합니다. 네트워크 경로를 검토하고 VM 간에 중간 홉 없이 직접 연결되었는지 확인합니다.

    > **참고**: 그 이유는 허브 가상 네트워크가 두 번째 스포크 가상 네트워크와 직접 피어링되기 때문입니다.

1. **Network Watcher - 연결 문제 해결** 페이지에서 다음 설정을 사용하여 한 번 더 확인을 시작합니다(다른 설정은 기본값으로 유지).

    > **참고**: 가상 머신 **az800l08-vm1** 의 브라우저 페이지를 새로 고쳐야 **가상 머신** 드롭다운 목록에 표시됩니다.

    | 설정 | 값 |
    | --- | --- |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | Resource group | **AZ800-L0801-RG** |
    | 소스 형식 | **가상 머신** |
    | 가상 머신 | **az800l08-vm1** |
    | 대상 | **수동으로 지정** |
    | URI, FQDN 또는 IPv4 | **10.82.0.4** |
    | 프로토콜 | **TCP** |
    | 대상 포트 | **3389** |

1. **확인** 을 선택하고 연결 확인 결과가 반환될 때까지 기다립니다. 상태는 **연결할 수 없음** 입니다.

    > **참고**: 그 이유는 두 스포크 가상 네트워크가 서로 피어링되지 않고 가상 네트워크 피어링이 전이적이지 않기 때문입니다.

#### <a name="task-4-configure-routing-in-the-hub-and-spoke-topology"></a>작업 4: 허브 및 스포크 토폴로지에서 라우팅 구성

1. Azure Portal에서 **가상 머신** 을 검색하여 선택합니다.
1. **가상 머신** 페이지의 가상 머신 목록에서 **az800l08-vm0** 을 선택합니다.
1. **az800l08-vm0** 가상 머신 페이지의 **설정** 섹션에서 **네트워킹** 을 선택합니다.
1. **네트워크 인터페이스** 레이블 옆의 **az800l08-nic0** 링크를 선택한 다음, **az800l08-nic0** 네트워크 인터페이스 페이지의 **설정** 섹션에서 **IP 구성** 을 선택합니다.
1. **IP 전달** 을 **사용** 으로 설정하고 **저장** 을 선택하여 변경 사항을 저장합니다.

   > **참고**: **az800l08-vm0** 이 라우터로 작동하려면 이 설정이 필요하며, 이 설정은 두 스포크 가상 네트워크 간에 트래픽을 라우팅합니다.

   > **참고**: 이제 라우팅을 지원하도록 **az800l08-vm0** 가상 머신의 운영 체제를 구성해야 합니다.

1. Azure Portal에서 **az800l08-vm0** Azure 가상 머신 페이지로 다시 이동합니다.
1. **az800l08-vm0** 페이지의 **작업** 섹션에서 **명령 실행** 을 선택한 다음, 명령 목록에서 **RunPowerShellScript** 를 선택합니다.
1. **명령 스크립트 실행** 페이지에서 다음 명령을 입력한 다음 **실행** 을 선택하여 원격 액세스 Windows Server 역할을 설치합니다.

   ```powershell
   Install-WindowsFeature RemoteAccess -IncludeManagementTools
   ```

   > **참고**: 명령이 성공적으로 완료되었음이 확인될 때까지 기다립니다.

1. **명령 실행 스크립트** 페이지의 **PowerShell 스크립트** 섹션에서, 이전에 입력한 명령을 다음 명령으로 바꾼 다음 **실행** 을 선택하여 라우팅 역할 서비스를 설치합니다.

   ```powershell
   Install-WindowsFeature -Name Routing -IncludeManagementTools -IncludeAllSubFeature
   Install-WindowsFeature -Name "RSAT-RemoteAccess-Powershell"
   Install-RemoteAccess -VpnType RoutingOnly
   Get-NetAdapter | Set-NetIPInterface -Forwarding Enabled
   ```

   > **참고**: 명령이 성공적으로 완료되었음이 확인될 때까지 기다립니다.

   > **참고**: 이제 스포크 가상 네트워크에서 사용자 정의 경로를 만들고 구성해야 합니다.

1. Azure Portal에서 도구 모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **경로 테이블** 을 검색하고 선택한 다음, **경로 테이블** 페이지에서 **+ 만들기** 를 선택합니다.
1. 다음 설정을 사용하여 경로 테이블을 만듭니다(다른 사용자는 기본값으로 유지).

    | 설정 | 값 |
    | --- | --- |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | Resource group | **AZ800-L0801-RG** |
    | 위치 | 가상 네트워크를 만든 Azure 지역의 이름 |
    | 이름 | **az800l08-rt12** |
    | 게이트웨이 경로 전파 | **아니요** |

1. **검토 및 만들기** 를 선택한 후 **만들기** 를 선택합니다.

   > **참고**: 라우팅 테이블이 만들어질 때까지 기다립니다. 이 작업은 1분 정도 걸립니다.

1. **리소스로 이동** 을 선택합니다.
1. **az800l08-rt12** 경로 테이블 페이지의 **설정** 섹션에서 **경로** 를 선택한 다음 **+ 추가** 를 선택합니다.
1. 다음 설정을 사용하여 새 경로를 추가합니다.

    | 설정 | 값 |
    | --- | --- |
    | 경로 이름 | **az800l08-route-vnet1-to-vnet2** |
    | 주소 접두사 | **10.82.0.0/20** |
    | 다음 홉 유형 | **가상 어플라이언스** |
    | 다음 홉 주소 | **10.80.0.4** |

    > **참고**: **10.80.0.4** 는 **az800l08-vm0** 의 개인 IP 주소를 나타냅니다. 

1. **확인** 을 선택합니다.
1. 다시 **az800l08-rt12** 경로 테이블 페이지의 **설정** 섹션에서 **서브넷** 을 선택한 다음 **+ 연결** 을 선택합니다.
1. 경로 테이블 **az800l08-rt12** 를 다음 서브넷과 연결합니다.

    | 설정 | 값 |
    | --- | --- |
    | 가상 네트워크 | **az800l08-vnet1** |
    | 서브넷 | **subnet0** |

1. **확인** 을 선택합니다.
1. **경로 테이블** 페이지로 돌아가 **+ 만들기** 를 선택합니다.
1. 다음 설정을 사용하여 경로 테이블을 만듭니다(다른 사용자는 기본값으로 유지).

    | 설정 | 값 |
    | --- | --- |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | Resource group | **AZ800-L0801-RG** |
    | 지역 | 가상 네트워크를 만든 Azure 지역의 이름 |
    | 이름 | **az800l08-rt21** |
    | 게이트웨이 경로 전파 | **아니요** |

1. **검토 및 만들기** 를 선택한 후 **만들기** 를 선택합니다.

   > **참고**: 라우팅 테이블이 만들어질 때까지 기다립니다. 이 작업은 3분 정도 걸립니다.

1. **리소스로 이동** 을 선택합니다.
1. **az800l08-rt21** 경로 테이블 페이지의 **설정** 섹션에서 **경로** 를 선택한 다음 **+ 추가** 를 선택합니다.
1. 다음 설정을 사용하여 새 경로를 추가합니다.

    | 설정 | 값 |
    | --- | --- |
    | 경로 이름 | **az800l08-route-vnet2-to-vnet1** |
    | 주소 접두사 | **10.81.0.0/20** |
    | 다음 홉 유형 | **가상 어플라이언스** |
    | 다음 홉 주소 | **10.80.0.4** |

1. **확인** 을 선택합니다.
1. 다시 **az800l08-rt21** 경로 테이블 페이지의 **설정** 섹션에서 **서브넷** 을 선택한 다음 **+ 연결** 을 선택합니다.
1. 경로 테이블 **az800l08-rt21** 를 다음 서브넷과 연결합니다.

    | 설정 | 값 |
    | --- | --- |
    | 가상 네트워크 | **az800l08-vnet2** |
    | 서브넷 | **subnet0** |

1. **확인** 을 선택합니다.
1. Azure Portal에서 **Network Watcher - 연결 문제 해결** 페이지로 다시 이동합니다.
1. **Network Watcher - 연결 문제 해결** 페이지에서 다음 설정을 사용하여 확인을 시작합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 |
    | --- | --- |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | Resource group | **AZ800-L0801-RG** |
    | 소스 형식 | **가상 머신** |
    | 가상 머신 | **az800l08-vm1** |
    | 대상 | **수동으로 지정** |
    | URI, FQDN 또는 IPv4 | **10.82.0.4** |
    | 프로토콜 | **TCP** |
    | 대상 포트 | **3389** |

1. **확인** 을 선택하고 연결 확인 결과가 반환될 때까지 기다립니다. 상태가 **연결할 수 있음** 인지 확인합니다. 네트워크 경로를 검토하고 트래픽이 **az800l08-nic0** 네트워크 어댑터에 할당된 **10.80.0.4** 를 통해 라우팅되었는지 확인합니다. 

    > **참고**: 그 이유는 이제 스포크 가상 네트워크 간의 트래픽이 라우터로 작동하는 허브 가상 네트워크에 있는 가상 머신을 통해 라우팅되기 때문입니다.


### <a name="exercise-2-implement-dns-name-resolution-in-azure"></a>연습 2: Azure에서 DNS 이름 확인 구현

#### <a name="task-1-configure-azure-private-dns-name-resolution"></a>작업 1: Azure 프라이빗 DNS 이름 확인 구성

1. **SEA-ADM1** 에서, Azure Portal을 표시하는 Microsoft Edge 창의 도구 모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **프라이빗 DNS 영역** 을 검색하여 선택한 다음, **프라이빗 DNS 영역** 페이지에서 **+ 만들기** 를 선택합니다.
1. 다음 설정을 사용하여 프라이빗 DNS 영역을 만듭니다.

    | 설정 | 값 |
    | --- | --- |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | Resource group | 새 리소스 그룹 **AZ800-L0802-RG** 의 이름 |
    | 이름 | **contoso.org** |
    | 리소스 그룹 위치 | 이 랩의 이전 연습에서 리소스를 배포하는 동일한 Azure 지역 |

1. **검토 및 만들기** 를 선택한 후 **만들기** 를 선택합니다.

    >**참고**: 프라이빗 DNS 영역이 만들어질 때까지 기다립니다. 이 작업은 2분 정도 걸립니다.

1. **리소스로 이동** 을 선택하여 **contoso.org** DNS 프라이빗 영역 페이지를 엽니다.
1. **contoso.org** 프라이빗 DNS 영역 페이지의 **설정** 섹션에서 **가상 네트워크 링크** 를 선택합니다.
1. **contoso.org \| 가상 네트워크 링크** 페이지에서 **+ 추가** 를 선택하고, 다음 설정을 지정하고(다른 설정을 기본값으로 유지) **확인** 을 선택하여 이전 연습에서 만든 첫 번째 가상 네트워크의 가상 네트워크 링크를 만듭니다.

    | 설정 | 값 |
    | --- | --- |
    | 링크 이름 | **az800l08-vnet0-link** |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | 가상 네트워크 | **az800l08-vnet0(AZ800-L0801-RG)** |
    | 자동 등록 사용 | 선택 |

    >**참고:** 가상 네트워크 링크가 만들어질 때까지 기다립니다. 이는 1분 이내에 완료됩니다.

1. 이전 단계를 반복하여 가상 네트워크 **az800l08-vnet1** 및 **az800l08-vnet2** 에 각각 **az800l08-vnet1-link** 및 **az800l08-vnet2-link** 는 이름의 가상 네트워크 링크(자동 등록 사용)를 만듭니다.
1. **contoso.org** 프라이빗 DNS 영역 페이지의 왼쪽 세로 메뉴에서 **개요** 를 선택합니다.
1. **contoso.org** 프라이빗 DNS 영역 페이지의 **개요** 섹션에서 DNS레코드 집합 목록을 검토하고 **az800l08-vm0**, **az800l08-vm1** 및 **az800l08-vm2** 의 **A** 레코드가 목록에서 **자동 등록됨** 으로 표시되는지 확인합니다.

    >**참고:** 레코드 집합이 나열되지 않은 경우 몇 분 정도 기다렸다가 페이지를 새로 고쳐야 할 수 있습니다.

#### <a name="task-2-validate-azure-private-dns-name-resolution"></a>작업 2: Azure 프라이빗 DNS 이름 확인 유효성 검사

1. **SEA-ADM1** 에서, Azure Portal을 표시하는 Microsoft Edge 창에서 **Network Watcher - 연결 문제 해결 페이지** 로 돌아갑니다.
1. **Network Watcher - 연결 문제 해결** 페이지에서 다음 설정을 사용하여 확인을 시작합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 |
    | --- | --- |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | Resource group | **AZ800-L0801-RG** |
    | 소스 형식 | **가상 머신** |
    | 가상 머신 | **az800l08-vm1** |
    | 대상 | **수동으로 지정** |
    | URI, FQDN 또는 IPv4 | **az800l08-vm2.contoso.org** |
    | 기본 설정 IP 버전 | **IPv4** |
    | 프로토콜 | **TCP** |
    | 대상 포트 | **3389** |

1. **확인** 을 선택하고 연결 확인 결과가 반환될 때까지 기다립니다. 상태가 **연결할 수 있음** 인지 확인합니다. 

    > **참고**: 그 이유는 대상 FQDN(정규화된 도메인 이름)을 Azure 프라이빗 DNS 영역을 통해 확인할 수 있기 때문입니다. 

#### <a name="task-3-configure-azure-public-dns-name-resolution"></a>작업 3: Azure 공용 DNS 이름 확인 구성

1. **SEA-ADM1** 에서, Azure Portal을 표시하는 Microsoft Edge 창에서 새 탭을 열고 **https://www.godaddy.com/domains/domain-name-search** 로 이동합니다.
1. 도메인 이름 검색을 사용하여 현재 사용되지 않는 도메인 이름을 확인합니다.
1. **SEA-ADM1** 에서, Azure Portal을 표시하는 Microsoft Edge 창의 도구 모음에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **DNS 영역** 을 검색하여 선택한 다음, **DNS 영역** 페이지에서 **+ 만들기** 를 선택합니다.
1. **DNS 영역 만들기** 페이지에서 다음 설정을 지정합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 |
    | --- | --- |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | 리소스 그룹 | **AZ800-L0802-RG** |
    | 이름 | 이 작업의 앞부분에서 확인한 DNS 도메인 이름 |

1. **검토 및 만들기** 를 선택한 후 **만들기** 를 선택합니다.

    >**참고**: DNS 영역이 만들어질 때까지 기다립니다. 이 작업은 1분 정도 걸립니다.

1. **리소스로 이동** 을 선택하여 새로 만든 DNS 영역의 페이지를 엽니다.
1. DNS 영역 페이지에서 **+ 레코드 집합** 을 선택합니다.
1. 레코드 집합 추가 창에서 다음 설정을 지정합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 |
    | --- | --- |
    | 이름 | **www** |
    | 형식 | **A** |
    | 별칭 레코드 집합 | **아니요** |
    | TTL | **1** |
    | TTL 단위 | **Hours** |
    | IP 주소 | 20.30.40.50 |

    >**참고**: IP 주소와 해당 이름은 전적으로 임의입니다. DNS 등록 기관에서 네임스페이스를 구매해야 하는 실제 시나리오를 에뮬레이트하는 대신 공용 DNS 레코드 구현을 보여주는 매우 간단한 예제를 제공하기 위한 것입니다. 

1. **확인** 을 선택합니다.
1. DNS 영역 페이지에서 **이름 서버 1** 의 전체 이름을 확인합니다.

    >**참고**: **이름 서버 1** 의 전체 이름을 기록합니다. 다음 작업에서 필요합니다.

#### <a name="task-4-validate-azure-public-dns-name-resolution"></a>작업 4: Azure 공용 DNS 이름 확인 유효성 검사

1. **SEA-ADM1** 의 **시작** 메뉴에서 **Windows PowerShell** 을 선택합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter를 눌러 새로 만든 DNS 영역에 설정된 **www** DNS 레코드의 외부 이름 확인을 테스트합니다(자리 표시자 `<Name server 1>`를 이 작업 앞부분에서 기록한 **이름 서버 1** 의 이름으로 바꾸고 `<domain name>` 자리 표시자를 앞부분에서 만든 DNS 도메인의 이름으로 바꿉니다.)

   ```powershell
   nslookup www.<domain name> <Name server 1>
   ```

1. 명령의 출력에 **20.30.40.50** 의 공용 IP 주소가 포함되어 있는지 확인합니다.

    >**참고**: **nslookup** 명령을 사용하면 레코드를 쿼리할 DNS 서버의 IP 주소를 지정할 수 있으므로 이름 확인이 예상대로 작동합니다(이 경우 `<Name server 1>`). 공개적으로 액세스할 수 있는 DNS 서버를 쿼리할 때 이름 확인이 작동하려면 DNS 등록 기관에 도메인 이름을 등록하고 Azure Portal의 공용 DNS 영역 페이지에 나열된 이름 서버를 도메인에 해당하는 네임스페이스에 대한 권한으로 구성해야 합니다.

## <a name="exercise-3-deprovisioning-the-azure-environment"></a>연습 3: Azure 환경 프로비전 해제

#### <a name="task-1-start-a-powershell-session-in-cloud-shell"></a>작업 1: Cloud Shell에서 PowerShell 세션 시작

1. **SEA-ADM1** 에서 Azure Portal이 표시된 Microsoft Edge 창으로 전환합니다.
1. Azure Portal이 표시된 Microsoft Edge 창에서 Cloud Shell 아이콘을 선택하여 Cloud Shell 창을 엽니다.

#### <a name="task-2-identify-all-azure-resources-provisioned-in-the-lab"></a>작업 2: 랩에서 프로비전한 모든 Azure 리소스 식별

1. Cloud Shell 창에서 다음 명령을 실행하여 이 랩 전체에서 만든 리소스 그룹을 모두 나열합니다.

   ```powershell
   Get-AzResourceGroup -Name 'az800l08*'
   ```

1. 다음 명령을 실행하여 이 랩 전체에서 만든 리소스 그룹을 모두 삭제합니다.

   ```powershell
   Get-AzResourceGroup -Name 'az800l08*' | Remove-AzResourceGroup -Force -AsJob
   ```

   >**참고**: 이 명령은 *-AsJob* 매개 변수에 의해 결정되어 비동기로 실행되므로, 동일한 PowerShell 세션 내에서 이 명령을 실행한 직후 다른 PowerShell 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지는 몇 분 정도 걸립니다.
