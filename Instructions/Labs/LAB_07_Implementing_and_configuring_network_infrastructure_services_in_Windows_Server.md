---
lab:
  title: '랩: Windows Server에서 네트워크 인프라 서비스 구현 및 구성'
  module: 'Module 7: Network Infrastructure services in Windows Server'
ms.openlocfilehash: d50ffa7e7dc2631ee6c955b27e8b3507479ee8c9
ms.sourcegitcommit: 33fdeedf81ac2a39e09176f7a4b7a72b983a072f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2022
ms.locfileid: "140742070"
---
# <a name="lab-implementing-and-configuring-network-infrastructure-services-in-windows-server"></a>랩: Windows Server에서 네트워크 인프라 서비스 구현 및 구성

## <a name="scenario"></a>시나리오

Contoso, Ltd.는 네트워크 서비스에 관한 복잡한 요구 사항이 있는 대형 조직입니다. 이러한 요구 사항을 충족하려면 DHCP를 배포하고 구성하여 서비스 가용성 보장 가능성을 높여야 합니다. Contoso의 한 부서인 Trey Research가 테스트 영역에 자체 DNS 서버를 가질 수 있도록 DNS도 설정해야 합니다. 마지막으로, Windows Admin Center에 대한 원격 액세스를 제공하고 웹 애플리케이션 프록시를 사용하여 액세스를 보호합니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음을 수행할 수 있습니다.

- DHCP를 배포하고 구성합니다.
- DNS를 배포하고 구성합니다.

## <a name="estimated-time-60-minutes"></a>예상 시간: 60분

## <a name="lab-setup"></a>랩 설정

가상 머신: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1** 과 **AZ-800T00A-ADM1** 을 실행해야 합니다. 다른 VM을 실행할 수도 있지만 이 랩에서는 필요하지 않습니다.

> **참고**: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1** 및 **AZ-800T00A-SEA-ADM1** 가상 머신은 **SEA-DC1**, **SEA-SVR1** 및 **SEA-ADM1** 의 설치를 호스팅합니다.

1. **SEA-ADM1** 을 선택합니다.
1. 다음 자격 증명을 사용하여 로그인합니다.

   - 사용자 이름: **Administrator**
   - 암호: **Pa55w.rd**
   - 도메인: **CONTOSO**

이 랩에서는 사용 가능한 VM 환경을 사용합니다.

## <a name="exercise-1-deploying-and-configuring-dhcp"></a>연습 1: DHCP 배포 및 구성

### <a name="scenario"></a>시나리오

Contoso의 Trey Research 하위 부서에는 사용자가 50여명에 불과한 별도의 사무실이 있습니다. 이들은 모든 컴퓨터에서 IP 주소를 수동으로 구성해왔으며, 이제 DHCP를 사용하길 원합니다. Trey Research 사이트의 범위를 적용하여 **SEA-SVR1** 에 DHCP를 설치합니다. 또한 **SEA-DC1** 를 이용한 고가용성을 위해 새 DHCP 서버를 사용하여 DHCP 장애 조치(failover)를 구성합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. DHCP 역할을 설치합니다.
1. DHCP 서버에 권한을 부여합니다.
1. 범위를 만듭니다.
1. DHCP 장애 조치(failover)를 구성합니다.
1. DHCP 기능을 확인합니다.

### <a name="task-1-install-the-dhcp-role"></a>작업 1: DHCP 역할 설치

1. **SEA-ADM1** 에서 관리자 권한으로 Windows PowerShell을 시작합니다.

   >**참고**: **SEA-ADM1** 에 Windows Admin Center를 아직 설치하지 않았다면 다음 두 단계를 수행합니다.

1. **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 Windows Admin Center 최신 버전을 다운로드합니다.
    
   ```powershell
   Start-BitsTransfer -Source https://aka.ms/WACDownload -Destination "$env:USERPROFILE\Downloads\WindowsAdminCenter.msi"
   ```
1. 다음 명령을 실행하여 Windows Admin Center를 설치합니다.
    
   ```powershell
   Start-Process msiexec.exe -Wait -ArgumentList "/i $env:USERPROFILE\Downloads\WindowsAdminCenter.msi /qn /L*v log.txt REGISTRY_REDIRECT_PORT_80=1 SME_PORT=443 SSL_CERTIFICATE_OPTION=generate"
   ```

   > **참고**: 설치가 완료될 때까지 기다리세요. 이 작업은 2분 정도 걸립니다.

1. **SEA-ADM1** 에서 Microsoft Edge 시작하고 `https://SEA-ADM1.contoso.com`에 있는 Windows Admin Center의 로컬 인스턴스에 연결합니다.

   >**참고**: 링크가 작동하지 않는다면 **SEA-ADM1** 에서 **WindowsAdminCenter.msi** 파일로 이동하여 상황에 맞는 메뉴를 연 다음 **복구** 를 선택하세요. 복구가 완료되면 Microsoft Edge를 새로 고칩니다. 
   
1. 메시지가 표시되면 **Windows 보안** 대화 상자에 다음 자격 증명을 입력한 다음 **확인** 을 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

1. Windows Admin Center에서 **sea-svr1.contoso.com** 에 연결을 추가하고 **Pa55w.rd** 암호를 사용하여 **CONTOSO\\Administrator** 자격으로 연결합니다.
1. **도구** 목록에서 **역할 및 기능을** 사용하여 **SEA-SVR1** 에 DHCP 역할을 설치합니다.
1. **도구** 목록에서 **DHCP** 도구로 이동하여 **DHCP PowerShell** 도구를 설치합니다. 

   > **참고**: **sea-svr1.contoso.com** 의 도구 창에서 **DHCP** 항목을 사용할 수 없다면 **Microsoft Edge를** 페이지를 새로 고친 다음 다시 시도하세요.

### <a name="task-2-authorize-the-dhcp-server"></a>작업 2: DHCP 서버에 권한 부여

1. **SEA-ADM1** 에서 서버 관리자를 엽니다.
1. 서버 관리자에서 **알림** 을 열고 **전체 DHCP 구성** 을 연 다음, 기본 옵션을 사용하여 **DHCP 설치 후 구성 마법사** 를 완료합니다.

### <a name="task-3-create-a-scope"></a>작업 3: 범위 만들기

1. **SEA-ADM1** 의 Windows Admin Center에서, **sea-svr1.contoso.com** 에 연결된 상태에서 **DHCP** 도구를 사용하여 다음 설정을 바탕으로 새 범위를 만듭니다.

   - 프로토콜: **IPv4**
   - 이름: **ContosoClients**
   - 시작 IP 주소: **10.100.150.50**
   - 끝 IP 주소: **10.100.150.254**
   - DHCP 클라이언트 서브넷 마스크: **255.255.255.0**
   - 라우터(기본 게이트웨이): **10.100.150.1**
   - DHCP 클라이언트 임대 기간: **4일**

1. 서버 관리자의 **도구** 메뉴에서 **DHCP 관리 콘솔** 을 엽니다.
1. **DHCP 관리 콘솔** 에서 권한 있는 서버(**172.16.10.12** 및 **SEA-DC1.contoso.com**)를 모두 추가합니다.
1. **DHCP 관리 콘솔** 에서 DHCP 서버 **172.16.10.12** 노드로 이동한 다음, **ContosoClients** 범위에서 값이 **172.16.10.10** 인 범위 옵션 **006 DNS 서버** 를 추가합니다.

### <a name="task-4-configure-dhcp-failover"></a>작업 4: DHCP 장애 조치(failover) 구성

1. **SEA-ADM1** 의 **DHCP** 관리 콘솔에서 DHCP 서버 **172.16.10.12** 의 **IPv4** 노드로 이동한 후, 다음 설정을 사용하여 **ContosoClients** 범위의 장애 조치(failover)를 **SEA-DC1.contoso.com** 으로 구성합니다.

   - 관계 이름: **SEA-SVR1 to SEA-DC1**
   - 최대 클라이언트 리드 타임: **1시간**
   - 모드: **상시 대기**
   - 파트너 서버의 역할: **대기**
   - 대기 서버용으로 예약된 주소: **5%**
   - 상태 전환 간격: **사용 안 함**
   - 메시지 인증 사용: **사용**
   - 공유 비밀: **DHCP-Failover**

1. **SEA-SVR1** 에 범위가 하나만 있는지 확인합니다.
1. 이제 **SEA-DC1** 에 범위가 두 개 있는지 확인합니다.
1. **SEA-ADM1** 의 **DHCP** 관리 콘솔에서 DHCP 서버 **SEA-DC1.contoso.com** 의 **IPv4** 노드로 이동한 다음, 기존 장애 조치(failover) 관계의 설정을 사용하여 **Contoso** 범위의 장애 조치(failover)를 **172.16.10.12** 로 구성합니다.
1. 이제 두 범위 모두가 **172.16.10.12**(**SEA-SVR1**)의 **IPv4** 노드 내에 표시되는지 확인합니다.

### <a name="task-5-verify-dhcp-functionality"></a>작업 5: DHCP 기능 확인

1. **SEA-ADM1** 에서 IP 구성을 정적 할당에서 동적 할당으로 변경합니다.
1. 결과 IP 구성을 검사하고 DHCP 임대를 **SEA-SVR1(172.16.10.12)** 에서 얻었는지 확인합니다.
1. **SEA-ADM1** 의 **DHCP 관리 콘솔** 에서 두 DHCP 서버 모두에서 **Contoso** 범위에 **SEA-ADM1** 의 임대가 나열되는지 확인합니다.
1. **SEA-ADM1** 에서 **DHCP 관리 콘솔** 을 사용하여 **SEA-SVR1(172.16.10.12)** 에서 **DHCP** 서비스를 중지합니다.
1. **SEA-ADM1** 에서 이더넷 네트워크 연결을 비활성화한 다음 다시 활성화하여 임대를 강제로 갱신합니다.
1. **SEA-ADM1** 에서 동일한 DHCP 임대를 **SEA-DC1(172.16.10.10)** 에서 얻었는지 확인합니다.

## <a name="exercise-2-deploying-and-configuring-dns"></a>연습 2: DNS 배포 및 구성

### <a name="scenario"></a>시나리오

Contoso의 한 Trey Research 위치에서 근무하는 직원은 자체 DNS 서버를 통해 테스트 환경에서 레코드를 만들어야 합니다. 하지만 이들의 테스트 환경에서는 여전히 Contoso의 인터넷 DNS 이름과 리소스 레코드를 확인할 수 있어야 합니다. 이러한 요구 사항을 충족하기 위해 전달을 ISP(인터넷 서비스 공급자)로 구성하고 **SEA-DC1** 으로 전달되는 **contoso.com** 의 조건부 전달자를 만듭니다. 또한 사용자 위치에 따라 다른 IP 주소를 확인해야 하는 테스트 애플리케이션도 있습니다. 본사 사용자의 경우에는 다른 방식으로 확인할 수 있도록, DNS 정책을 사용하여 **testapp.treyresearch.net** 을 구성합니다.

이 연습의 주요 작업은 다음과 같습니다.

1. DNS 역할을 설치합니다.
1. DNS 영역을 만듭니다.
1. 전달을 구성합니다.
1. 조건부 전달을 구성합니다.
1. DNS 정책을 구성합니다.
1. DNS 정책 기능을 확인합니다.

### <a name="task-1-install-the-dns-role"></a>작업 1: DNS 역할 설치

1. **SEA-ADM1** 에서 **sea-svr1.contoso.com** 에 연결된 Windows Admin Center의 **도구** 목록에 있는 **역할 및 기능** 을 사용하여 **SEA-SVR1** 에 DNS 역할을 설치합니다.
1. **도구** 목록에서 **DNS** 도구로 이동하여 **DNS PowerShell** 도구를 설치합니다. 

   > **참고**: **sea-svr1.contoso.com** 의 **도구** 목록에서 **DNS** 항목을 사용할 수 없다면 **Microsoft Edge를** 페이지를 새로 고친 다음 다시 시도하세요.

### <a name="task-2-create-a-dns-zone"></a>작업 2: DNS 영역 만들기

1. **SEA-ADM1** 의 Windows Admin Center에서, **sea-svr1.contoso.com** 에 연결된 상태에서 **DNS** 도구를 사용하여 다음 설정을 바탕으로 새 DNS 영역을 만듭니다.

   - 영역 유형: **기본**
   - 영역 이름: **TreyResearch.net**
   - 영역 파일: **새 파일 만들기**
   - 영역 파일 이름: **TreyResearch.net.dns**
   - 동적 업데이트: **동적 업데이트 허용 안 함**

1. Windows Admin Center에서, **sea-svr1.contoso.com** 에 연결된 상태에서 **DNS** 도구를 사용하여 다음 설정을 바탕으로 **TreyResearch.net** 영역에 새 DNS 레코드를 만듭니다.

   - DNS 레코드 유형: **호스트(A)**
   - 레코드 이름: **TestApp**
   - IP 주소: **172.30.99.234**
   - TTL(Time to Live): **600**

1. **SEA-ADM1** 의 **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 새로 만든 레코드가 올바르게 확인되는지 확인합니다.

    ```powershell
    Resolve-DnsName -Server sea-svr1.contoso.com -Name testapp.treyresearch.net
    ```

### <a name="task-3-configure-forwarding"></a>작업 3: 전달 구성

1. **SEA-ADM1** 의 서버 관리자 **도구** 메뉴에서 **DNS 관리자 콘솔** 을 엽니다.
1. **DNS 관리자** 에서 **SEA-SVR1.contoso.com** 에 연결합니다.
1. **SEA-SVR1.contoso.com** 속성의 **전달자** 탭에서 **131.107.0.100** 을 전달자로 구성합니다.

### <a name="task-4-configure-conditional-forwarding"></a>작업 4: 조건부 전달 구성

1. **SEA-ADM1** 의 **DNS 관리자** 에서, SEA-**SVR1.contoso.com** 에 연결된 상태에서 **Contoso.com** 을 위한 새 조건부 전달자를 만듭니다. 이 전달자는 요청을 **SEA-DC1.contoso.com**(**172.16.10.10**)에 전달합니다.
1. **SEA-ADM1** 의 **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 조건부 전달자가 작동하는지 확인합니다.

    ```powershell
    Resolve-DnsName -Server sea-svr1.contoso.com -Name sea-dc1.contoso.com
    ```

### <a name="task-5-configure-dns-policies"></a>작업 5: DNS 정책 구성

1. **SEA-ADM1** 의 Windows Admin Center에서, **sea-svr1.contoso.com** 에 연결된 상태에서 **도구** 목록에 있는 **PowerShell** 을 사용하여 PowerShell 원격 작업 세션을 설정합니다.
1. **Windows PowerShell** 프롬프트에서 다음 명령을 실행하여 본사 서브넷을 만듭니다.

    ```powershell
    Add-DnsServerClientSubnet -Name "HeadOfficeSubnet" -IPv4Subnet "172.16.10.0/24"
    ```

1. 다음 명령을 실행하여 본사의 영역 범위를 만듭니다.

    ```powershell
    Add-DnsServerZoneScope -ZoneName "TreyResearch.net" -Name "HeadOfficeScope"
    ```

1. 다음 명령을 실행하여 본사 범위에 대한 새 리소스 레코드를 만듭니다.

    ```powershell
    Add-DnsServerResourceRecord -ZoneName "TreyResearch.net" -A -Name "testapp" -IPv4Address "172.30.99.100" -ZoneScope "HeadOfficeScope"
    ```

1. 다음 명령을 실행하여 본사 서브넷과 영역 범위를 연결하는 새 정책을 만듭니다.

    ```powershell
    Add-DnsServerQueryResolutionPolicy -Name "HeadOfficePolicy" -Action ALLOW -ClientSubnet "eq,HeadOfficeSubnet" -ZoneScope "HeadOfficeScope,1" -ZoneName "TreyResearch.net"
    ```

### <a name="task-6-verify-dns-policy-functionality"></a>작업 6: DNS 정책 기능 확인

1. **SEA-ADM1** 의 **Windows PowerShell** 콘솔에서 `ipconfig`를 실행하여 **SEA-ADM1** 이 **HeadOfficeSubnet(172.16.10.0)** 에 있는지 확인합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 DNS 정책을 테스트합니다.

    ```powershell
    Resolve-DnsName -Server sea-svr1.contoso.com -Name testapp.treyresearch.net
    ```

   > **참고**: 이름이 **HeadOfficePolicy** 에 구성했던 IP 주소인 **172.30.99.100** 으로 확인되는지 확인합니다.

1. **172.16.10.11** 에서 **SEA-ADM1** 에 할당한 IP 주소를 **HeadOfficeSubnet** 의 IP 주소 범위 내에 존재하지 않는 IP 주소(**172.16.11.11**)로 변경합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 실행하여 DNS 정책을 테스트합니다.

    ```powershell
    Resolve-DnsName -Server sea-svr1.contoso.com -Name testapp.treyresearch.net
    ```
   > **참고**: 이름이 **172.30.99.234** 로 확인되는지 확인합니다. **SEA-ADM1** 의 IP 주소가 더 이상 **HeadOfficeSubnet** 내에 존재하지 않기 때문에 이 주소로 확인될 것입니다. `testapp.treyresearch.net`을 대상으로 하는 **(172.16.10.0/24)** 의 **HeadOfficeSubnet** 에서 시작된 DNS 쿼리는 **172.30.99.100** 으로 확인됩니다. `testapp.treyresearch.net`을 대상으로 하는 이 서비스 외부에 있는 DNS 쿼리는 **172.30.99.234** 로 확인됩니다.

1. **SEA-ADM1** 의 IP 주소를 원래 값으로 되돌립니다.
