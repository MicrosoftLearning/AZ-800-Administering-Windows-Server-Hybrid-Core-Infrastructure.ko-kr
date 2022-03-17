---
lab:
  title: '랩: Windows Server에서 네트워크 인프라 서비스 구현 및 구성'
  type: Answer Key
  module: 'Module 7: Network Infrastructure services in Windows Server'
ms.openlocfilehash: b07da01785e12c2d025ba76fa3e712fdd35b781a
ms.sourcegitcommit: bd43c7961e93ef200b92fb1d6f09d9ad153dd082
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2022
ms.locfileid: "137907045"
---
# <a name="lab-answer-key-implementing-and-configuring-network-infrastructure-services-in-windows-server"></a>랩 정답 키: Windows Server에서 네트워크 인프라 서비스 구현 및 구성

## <a name="exercise-1-deploying-and-configuring-dhcp"></a>연습 1: DHCP 배포 및 구성

#### <a name="task-1-install-the-dhcp-role"></a>작업 1: DHCP 역할 설치

1. **SEA-ADM1** 에 연결한 다음, 필요하다면 **Pa55w.rd** 암호를 이용해 **CONTOSO\\Administrator** 로 로그인합니다.
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

1. 모든 연결 창에서 **+ 추가** 를 선택합니다.
1. 리소스 추가 또는 만들기 창의 **서버** 타일에서 **추가** 를 선택합니다.
1. **서버 이름** 텍스트 상자에 **sea-svr1.contoso.com** 을 입력합니다. 
1. **이 연결에 다른 계정 사용** 옵션이 선택되어 있는지 확인하고 다음 자격 증명을 입력한 다음 **자격 증명을 사용하여 추가** 를 선택합니다.

   - 사용자 이름: **CONTOSO\\Administrator**
   - 암호: **Pa55w.rd**

   > **참고**: Single Sign-On을 수행하려면 Kerberos 제한 위임을 설정해야 합니다.

1. **sea-svr1.contoso.com** 페이지의 **도구** 목록에서 **역할 및 기능을** 선택합니다.
1. 역할 및 기능 창에서 **DHCP 서버** 확인란을 선택한 다음 **+ 설치** 를 선택합니다.
1. 역할 및 기능 설치 창에서 **예** 를 선택합니다.

   > **참고**: DHCP 역할이 설치되었음을 나타내는 알림이 표시될 때까지 기다리세요. 필요한 경우 **알림** 아이콘을 선택하여 현재 상태를 확인합니다.

1. **sea-svr1.contoso.com** 페이지로 돌아가서 **Microsoft Edge** 페이지를 새로 고치고 **도구** 목록에서 **DHCP** 를 선택한 다음, 세부 정보 창에서 **설치** 를 선택하여 DHCP PowerShell 도구를 설치합니다. 

   > **참고**: **sea-svr1.contoso.com** 의 **도구** 목록에서 **DHCP** 항목을 사용할 수 없다면 **Microsoft Edge** 페이지를 새로 고친 다음 다시 시도하세요.

1. DHCP PowerShell 도구가 설치되었다는 알림을 기다립니다. 필요한 경우 **알림** 아이콘을 선택하여 현재 상태를 확인합니다.

#### <a name="task-2-authorize-the-dhcp-server"></a>작업 2: DHCP 서버에 권한 부여

1. **SEA-ADM1** 에서 **시작을** 선택한 다음 **서버 관리자** 를 선택합니다.
1. **서버 관리자** 의 메뉴에서 **알림** 을 선택한 다음 **DHCP 구성 완료** 를 선택합니다.
1. **DHCP 설치 후 구성 마법사** 창의 **설명** 화면에서 **다음** 을 선택합니다.
1. **권한 부여** 화면에서 **CONTOSO\\Administrator** 옵션이 선택되어 있는지 확인하고 **커밋** 을 선택합니다.
1. 두 작업을 모두 완료했으면 **닫기** 를 선택합니다.

#### <a name="task-3-create-a-scope"></a>작업 3: 범위 만들기

1. **SEA-ADM1** 에서 **SEA-SVR1** 의 **DHCP** 설정이 표시된 Microsoft Edge 창에서 Windows Admin Center로 전환합니다.
1. **DHCP** 페이지에서 **+ 새 범위** 를 선택합니다.
1. 새 범위 만들기 창에서 다음 설정을 지정한 다음 **만들기** 를 선택합니다.

   - 프로토콜: **IPv4**
   - 이름: **ContosoClients**
   - 시작 IP 주소: **10.100.150.50**
   - 끝 IP 주소: **10.100.150.254**
   - DHCP 클라이언트 서브넷 마스크: **255.255.255.0**
   - 라우터(기본 게이트웨이): **10.100.150.1**
   - DHCP 클라이언트 임대 기간: **4일**

1. **SEA-ADM1** 에서 **서버 관리자** 로 전환하고 **, 서버 관리자** 에서 **도구** 를 선택한 다음 **DHCP** 를 선택합니다.
1. **DHCP** 창의 작업 창에서 **추가 작업** 을 선택한 다음 **권한 있는 서버 관리** 를 선택합니다.
1. **권한 있는 서버 관리** 창에서 **새로 고침** 을 선택하고 권한 있는 DHCP 서버 목록에 **sea-svr1.contoso.com** 이 표시되는지 확인한 다음 **권한 있는 서버 관리** 창을 닫습니다.
1. **DHCP** 창의 작업 창에서 **추가 작업** 을 선택한 다음 **서버 추가** 를 선택합니다.
1. **서버 추가** 대화 상자에서 **이 권한 있는 DHCP 서버** 를 선택하고 **sea-svr1.contoso.com** 을 선택한 다음 **확인** 을 선택합니다.
1. **DHCP** 창에서 **172.16.10.12**, **IPv4**, **범위 [10.100.150.0] ContosoClients** 를 차례로 확장한 다음 **범위 옵션** 을 선택합니다.
1. 작업 창에서 **추가 작업** 을 선택한 다음 **옵션 구성** 을 선택합니다.
1. **범위 옵션** 대화 상자에서 **006 DNS 서버** 확인란을 선택합니다.
1. **서버 이름** 텍스트 상자에 **sea-dc1.contoso.com** 입력하고, **확인** 을 선택하고, 이름이 **172.16.10.10** 으로 확인되었는지 확인하고, **추가** 를 선택한 다음 **확인** 을 선택합니다.

#### <a name="task-4-configure-dhcp-failover"></a>작업 4: DHCP 장애 조치(failover) 구성

1. **SEA-ADM1** 의 **DHCP** 창에서 **IPv4** 를 선택하고 작업 창에서 **추가 작업** 을 선택한 다음 **장애 조치(failover) 구성** 을 선택합니다.
1. **장애 조치(failover) 구성** 창에서 **모든 선택** 확인란이 선택되어 있는지 확인하고 **다음** 을 선택합니다.
1. **장애 조치(failover)에 사용할 파트너 서버 지정** 화면에서 **서버 추가** 를 선택합니다.
1. **서버 추가** 대화 상자에서 **이 권한 있는 DHCP 서버** 를 선택하고 **sea-dc1.contoso.com** 을 선택한 다음 **확인** 을 선택합니다.
1. **장애 조치(failover)에 사용할 파트너 서버 지정** 화면으로 돌아가서 **파트너 서버** 드롭다운 목록에 **sea-dc1** 이 표시되는지 확인하고 **다음** 을 선택합니다.
1. **새 장애 조치(failover) 관계 만들기** 화면에서 다음 정보를 입력하고 **다음** 을 선택합니다.

   - 관계 이름: **SEA-SVR1 to SEA-DC1**
   - 최대 클라이언트 리드 타임: **1시간**
   - 모드: **상시 대기**
   - 파트너 서버의 역할: **대기**
   - 대기 서버용으로 예약된 주소: **5%**
   - 상태 전환 간격: **사용 안 함**
   - 메시지 인증 사용: **사용**
   - 공유 비밀: **DHCP-Failover**

1. **마침** 을 선택합니다.
1. **장애 조치(failover) 구성** 대화 상자에서 **닫기** 를 선택합니다.
1. **DHCP** 창의 작업 창에서 **추가 작업** 을 선택한 다음 **서버 추가** 를 선택합니다.
1. **서버 추가** 대화 상자에서 **이 권한 있는 DHCP 서버** 를 선택하고 **sea-dc1.contoso.com** 을 선택한 다음 **확인** 을 선택합니다.
1. **SEA-ADM1** 의 **DHCP** 창에서 **sea-dc1** 노드를 확장하고 **IPv4** 를 선택한 다음 두 범위가 나열되는지 확인합니다.
1. **범위 [172.16.0.0] Contoso** 를 선택하고 작업 창에서 **추가 작업** 을 선택한 다음 **장애 조치(failover) 구성** 을 선택합니다.
1. **장애 조치(failover) 구성** 창에서 **다음** 을 선택합니다.
1. **장애 조치(failover)에 사용할 파트너 서버 지정** 화면의 **파트너 서버** 상자에 **172.16.10.12** 를 입력한 다음 **이 서버에 대해 구성된 기존 장애 조치(failover) 관계(있는 경우)를 다시 사용합니다.** 확인란을 선택하고 **다음** 을 선택합니다.
1. **이 서버에 대해 이미 구성된 장애 조치(failover) 관계에서 선택** 화면에서 **다음** 을 선택하고 **마침** 을 선택합니다.
1. **장애 조치(failover) 구성** 대화 상자에서 **닫기** 를 선택합니다.
1. **172.16.10.12** 에서 **IPv4** 를 선택한 다음 두 범위가 모두 나열되었는지 확인합니다. 필요한 경우 **F5** 키를 눌러 새로 고칩니다.

#### <a name="task-5-verify-dhcp-functionality"></a>작업 5: DHCP 기능 확인

1. **SEA-ADM1** 에서 **시작을** 선택한 다음 **설정** 을 선택합니다.
1. **설정** 창에서 **네트워크 및 인터넷** 을 선택한 다음 **네트워크 및 공유 센터** 를 선택합니다.
1. **네트워크 및 공유 센터** 에서 **이더넷** 을 선택한 다음 **속성** 을 선택합니다.
1. **이더넷 속성** 대화 상자에서 **Internet Protocol Version 4(TCP/IPv4)** 를 선택한 다음 **속성** 을 선택합니다.
1. **Internet Protocol Version 4(TCP/IPv4) 속성** 대화 상자에서 **자동으로 IP 주소 가져오기** 를 선택하고 **자동으로 DNS 서버 주소 가져오기** 를 선택한 다음 **확인** 을 선택합니다.
1. **닫기** 를 선택한 다음, **이더넷 상태** 창에서 **세부 정보** 를 선택합니다.
1. **네트워크 연결 세부 정보** 대화 상자에서 DHCP가 활성화되었는지, IP 주소를 얻었는지, **SEA-SVR2(172.16.10.12)** DHCP 서버에서 임대를 발급했는지 확인합니다.
1. **닫기** 를 선택하여 **이더넷 상태** 창으로 돌아갑니다.
1. **SEA-ADM1** 의 **DHCP** 창에서 **172.16.10.12** 노드를 확장하고, **IPv4** 노드를 확장하고, **범위 [172.16.0.0] Contoso** 노드를 확장한 다음 **주소 임대** 를 선택합니다.
1. **SEA-ADM1.contoso.com** 임대를 나타내는 항목이 있는지 확인합니다.
1. **SEA-ADM1** 의 **DHCP** 창에서 **sea-dc1** 노드를 확장하고, **IPv4** 노드를 확장하고, **범위 [172.16.0.0] Contoso** 노드를 확장한 다음 **주소 임대** 를 선택합니다.
1. 여기에도 **SEA-ADM1.contoso.com** 임대를 나타내는 항목이 있는지 확인합니다.
1. **172.16.10.12** 를 선택하고, 작업 창에서 **추가 작업** 을 선택하고, **모든 작업** 을 선택한 다음 **중지** 를 선택합니다.
1. **SEA-ADM1** 에서 **이더넷 상태** 창으로 다시 전환한 다음 **사용 안 함** 을 선택합니다.
1. **네트워크 및 공유 센터** 창으로 돌아가서 **어댑터 설정 변경을** 선택하고 **이더넷** 을 선택한 다음 **이 네트워크 디바이스 사용** 을 선택합니다.
1. 사용하도록 설정한 **이더넷** 연결을 두 번 클릭하여 상태 창을 표시합니다.
1. **이더넷 상태** 창에서 **세부 정보** 를 선택합니다.
1. **네트워크 연결 세부 정보** 대화 상자에서 DHCP가 활성화되었는지, IP 주소를 얻었는지, **SEA-DC1(172.16.10.10)** DHCP 서버에서 임대를 발급했는지 확인합니다.
1. **닫기** 를 선택하여 **이더넷 상태** 창으로 돌아갑니다.
1. **이더넷 상태** 창에서 **속성** 을 선택합니다.
1. **이더넷 속성** 대화 상자에서 **Internet Protocol Version 4(TCP/IPv4)** 를 선택한 다음 **속성** 을 선택합니다.
1. **Internet Protocol Version 4(TCP/IPv4) 속성** 대화 상자에서 **다음 IP 주소 사용** 을 선택하고 다음 설정을 지정합니다.

   - IP 주소: **172.16.10.11**
   - 서브넷 마스크: **255.255.0.0**
   - 기본 게이트웨이: **172.16.10.1**

1. **Internet Protocol Version 4(TCP/IPv4) 속성** 대화 상자에서 **다음 DNS 서버 주소 사용** 을 선택하고 **기본 DNS 서버** 를 **172.16.10.10** 으로 설정한 다음 **확인** 을 선택합니다.

   > **참고**: **이더넷 상태** 창을 열어 두세요. 이 랩의 후반부에서 필요합니다. 

## <a name="exercise-2-deploying-and-configuring-dns"></a>연습 2: DNS 배포 및 구성

#### <a name="task-1-install-the-dns-role"></a>작업 1: DNS 역할 설치

1. **SEA-ADM1** 에서 Windows Admin Center의 **sea-svr1.contoso.com** 에 대한 연결이 표시된 Microsoft Edge 창으로 전환합니다. 
1. **도구** 목록에서 **역할 및 기능** 을 선택합니다.
1. 역할 및 기능 창에서 **DNS 서버** 확인란을 선택한 다음 **+ 설치** 를 선택합니다.
1. 역할 및 기능 설치 창에서 **예** 를 선택합니다.

   > **참고**: DNS 역할이 설치되었음을 나타내는 알림이 표시될 때까지 기다리세요. 필요한 경우 **알림** 아이콘을 선택하여 현재 상태를 확인합니다.

1. **sea-svr1.contoso.com** 페이지로 돌아가서 **Microsoft Edge** 페이지를 새로 고치고 **도구** 목록에서 **DNS** 를 선택한 다음, 세부 정보 창에서 **설치** 를 선택하여 DNS PowerShell 도구를 설치합니다. 

   > **참고**: **sea-svr1.contoso.com** 의 **도구** 목록에서 **DNS** 항목을 사용할 수 없다면 **Microsoft Edge** 페이지를 새로 고친 다음 다시 시도하세요.

1. DNS PowerShell 도구가 설치되었다는 알림이 나타날 때까지 기다립니다. 필요한 경우 **알림** 아이콘을 선택하여 현재 상태를 확인합니다.

#### <a name="task-2-create-a-dns-zone"></a>작업 2: DNS 영역 만들기

1. **SEA-ADM1** 에서, Windows Admin Center의 DNS 창에서 **작업** 을 선택하고 **작업** 메뉴에서 **+ 새 DNS 영역 만들기** 를 선택합니다.
1. **새 DNS 영역 만들기** 대화 상자에서 다음 설정을 지정한 다음 **만들기** 를 선택합니다.

   - 영역 유형: **기본**
   - 영역 이름: **TreyResearch.net**
   - 영역 파일: **새 파일 만들기**
   - 영역 파일 이름: **TreyResearch.net.dns**
   - 동적 업데이트: **동적 업데이트 허용 안 함**

1. DNS 창으로 돌아가서 **TreyResearch.net** 을 선택한 다음 **+ 새 DNS 레코드 만들기** 를 선택합니다.
1. 새 DNS 레코드 만들기 창에서 다음 설정을 지정한 다음 **만들기** 를 선택합니다.

   - DNS 레코드 유형: **호스트(A)**
   - 레코드 이름: **TestApp**
   - IP 주소: **172.30.99.234**
   - TTL(Time to Live): **600**

1. **SEA-ADM1** 에서 **시작** 을 선택한 다음 **Windows PowerShell** 을 선택합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter 키를 눌러 새 DNS 레코드가 이름 확인을 제공하는지 확인합니다.

   ```powershell
   Resolve-DnsName -Server sea-svr1.contoso.com -Name testapp.treyresearch.net
   ```

#### <a name="task-3-configure-forwarding"></a>작업 3: 전달 구성

1. **SEA-ADM1** 에서 서버 관리자로 전환합니다.
1. 서버 관리자에서 **도구** 를 선택한 다음 **DNS** 를 선택합니다.
1. **DNS 관리자** 에서 **DNS** 를 선택하고 상황에 맞는 메뉴를 표시하여 **DNS 서버에 연결** 을 선택합니다.
1. **DNS 서버에 연결** 대화 상자에서 **다음 컴퓨터** 를 선택하고 **SEA-SVR1.contoso.com** 을 입력한 다음 **확인** 을 선택합니다.
1. **DNS 관리자** 에서 **SEA-SVR1.contoso.com** 을 선택하고 상황에 맞는 메뉴를 표시하여 **속성** 을 선택합니다.
1. **SEA-SVR1.contoso.com 속성** 대화 상자에서 **전달자** 탭을 선택한 다음 **편집** 을 선택합니다.
1. **전달자 편집** 대화 상자의 **전달 서버 IP 주소** 상자에 **131.107.0.100** 을 입력한 다음 **확인** 을 선택합니다.
1. **SEA-SVR1.contoso.com 속성** 대화 상자에서 **확인** 을 선택합니다.

#### <a name="task-4-configure-conditional-forwarding"></a>작업 4: 조건부 전달 구성

1. **SEA-ADM1** 의 **DNS 관리자** 에서 **SEA-SVR1.contoso.com** 을 확장한 다음 **조건부 전달자** 를 선택합니다.
1. **조건부 전달자** 를 선택하고 상황에 맞는 메뉴를 표시하여 **새 조건부 전달자** 를 선택합니다.
1. **새 조건부 전달자** 대화 상자의 **DNS 도메인** 상자에 **Contoso.com** 을 입력합니다.
1. **마스터 서버 IP 주소** 상자에 **172.16.10.10** 을 입력한 다음 **Enter** 키를 선택합니다.

   > **참고**: **새 조건부 전달자** 대화 상자의 유효성 검사 열에서 **알 수 없는 오류가 발생했습니다.** 메시지는 무시하세요.

1. **확인** 을 선택합니다.
1. **SEA-ADM1** 에서 **Windows PowerShell** 콘솔로 전환합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter 키를 눌러 조건부 전달을 검증합니다.

   ```powershell
   Resolve-DnsName -Server sea-svr1.contoso.com -Name sea-dc1.contoso.com
   ```

#### <a name="task-5-configure-dns-policies"></a>작업 5: DNS 정책 구성

1. **SEA-ADM1** 에서 Windows Admin Center의 **sea-svr1.contoso.com** 에 대한 연결이 표시된 Microsoft Edge 창으로 전환합니다.
1. **도구** 목록에서 **PowerShell** 을 선택하고 메시지가 표시되면 **CONTOSO\\Administrator** 사용자, **Pa55w.rd** 암호를 사용하여 로그인합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter 키를 눌러 본사 서브넷을 만듭니다.

   ```powershell
   Add-DnsServerClientSubnet -Name "HeadOfficeSubnet" -IPv4Subnet '172.16.10.0/24'
   ```
1. 다음 명령을 입력한 다음 Enter 키를 눌러 본사의 영역 범위를 만듭니다.

   ```powershell
   Add-DnsServerZoneScope -ZoneName 'TreyResearch.net' -Name 'HeadOfficeScope'
   ```
1. 다음 명령을 입력한 다음 Enter 키를 눌러 본사 범위에 대한 새 리소스 레코드를 추가합니다.

   ```powershell
   Add-DnsServerResourceRecord -ZoneName 'TreyResearch.net' -A -Name 'testapp' -IPv4Address '172.30.99.100' -ZoneScope 'HeadOfficeScope'
   ```
1. 다음 명령을 입력한 다음 Enter 키를 눌러 본사 서브넷과 영역 범위를 연결하는 새 정책을 만듭니다.

   ```powershell
   Add-DnsServerQueryResolutionPolicy -Name 'HeadOfficePolicy' -Action ALLOW -ClientSubnet 'eq,HeadOfficeSubnet' -ZoneScope 'HeadOfficeScope,1' -ZoneName 'TreyResearch.net'
   ```

#### <a name="task-6-verify-dns-policy-functionality"></a>작업 6: DNS 정책 기능 확인

1. **SEA-ADM1** 에서 **Windows PowerShell** 콘솔로 전환합니다.
1. **Windows PowerShell** 콘솔에서 `ipconfig`를 입력한 다음 Enter 키를 눌러 현재 IP 구성을 표시합니다.

   > **참고**: 이더넷 어댑터에는 정책에 구성된 **HeadOfficeSubnet** 의 일부인 IP 주소가 있습니다.

1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter 키를 눌러 `testapp.treyresearch.net` DNS 레코드의 확인을 테스트합니다.

   ```powershell
   Resolve-DnsName -Server sea-svr1.contoso.com -Name testapp.treyresearch.net
   ```

   > **참고**: 이름이 **HeadOfficePolicy** 에 구성했던 IP 주소인 **172.30.99.100** 으로 확인되는지 확인합니다.

1. **SEA-ADM1** 에서 **이더넷 상태** 창으로 다시 전환합니다.
1. **이더넷 상태** 창에서 **속성** 을 선택합니다.
1. **이더넷 속성** 대화 상자에서 **Internet Protocol Version 4(TCP/IPv4)** 를 선택한 다음 **속성** 을 선택합니다.
1. **Internet Protocol Version 4(TCP/IPv4) 속성** 대화 상자에서 현재 할당된 IP 주소(**172.16.10.11**)를 **HeadOfficeSubnet** 의 IP 주소 범위 내에 없는 IP 주소 **172.16.11.11** 로 변경한 다음 **확인** 을 선택합니다.
1. **SEA-ADM1** 에서 **Windows PowerShell** 콘솔로 전환합니다.
1. **Windows PowerShell** 콘솔에서 다음 명령을 입력한 다음 Enter 키를 눌러 `testapp.treyresearch.net` DNS 레코드의 확인을 테스트합니다.

   ```powershell
   Resolve-DnsName -Server sea-svr1.contoso.com -Name testapp.treyresearch.net
   ```

   > **참고**: 이름이 **172.30.99.234** 로 확인되는지 확인합니다. **SEA-ADM1** 의 IP 주소가 더 이상 **HeadOfficeSubnet** 내에 존재하지 않기 때문에 이 주소로 확인될 것입니다. `testapp.treyresearch.net`을 대상으로 하는 **(172.16.10.0/24)** 의 **HeadOfficeSubnet** 에서 시작된 DNS 쿼리는 **172.30.99.100** 으로 확인됩니다. `testapp.treyresearch.net`을 대상으로 하는 이 서비스 외부에 있는 DNS 쿼리는 **172.30.99.234** 로 확인됩니다.

   > **참고**: 이제 **SEA-ADM1** 의 IP 주소를 원래 값으로 되돌립니다.

1. **이더넷 상태** 창으로 다시 전환하고 **속성** 을 선택합니다.
1. **이더넷 속성** 대화 상자에서 **Internet Protocol Version 4(TCP/IPv4)** 를 선택한 다음 **속성** 을 선택합니다.
1. **Internet Protocol Version 4(TCP/IPv4) 속성** 대화 상자에서 현재 할당된 IP 주소(**172.16.11.11**)를 원래 값(**172.16.10.11**)으로 변경하고 **확인** 을 선택합니다.
1. **닫기** 를 두 번 선택합니다.
1. 열려 있는 창을 모두 닫습니다.
