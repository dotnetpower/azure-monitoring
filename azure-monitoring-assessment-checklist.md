# Azure Monitoring 상세 점검 체크리스트

이 문서는 [Azure Monitoring 2시간 점검 플랜](azure-monitoring-assessment-plan.md)을 수행하면서 무엇을 확인하고, Azure Portal의 어디에서 근거를 수집할지 정리한 문서입니다. 점검 결과를 `이슈 사항`, `권고 사항`, `근거 화면` 중심의 PowerPoint로 작성하는 것을 전제로 합니다.

점검 항목은 Azure Monitor 기능 기준 항목에 더해 Microsoft 공식 모범 사례(Azure Monitor Logs·Alerts·비용 최적화 모범 사례, Azure Well-Architected Framework Log Analytics 가이드, Azure Monitor Baseline Alerts)를 반영했습니다. 항목별 근거는 [7. Microsoft 모범 사례 근거 매핑](#7-microsoft-모범-사례-근거-매핑)을 참고합니다.

## 1. 사용 방법

1. 점검 대상 Subscription, Resource Group과 핵심 서비스를 먼저 확정합니다.
2. **중요도 상** 항목을 먼저 확인하고 시간이 남으면 중, 하 순서로 진행합니다.
3. 각 항목을 `정상`, `개선 필요`, `해당 없음` 중 하나로 판정합니다.
4. `개선 필요` 항목은 근거 화면과 함께 이슈 및 권고 사항으로 기록합니다.
5. 중요도 상 이슈는 한 슬라이드에 한 건, 중과 하는 관련 항목을 묶어 작성합니다.

Portal 메뉴 이름은 언어 설정과 리소스 종류에 따라 일부 다르게 표시될 수 있습니다.

## 2. 중요도와 결과 판정 기준

### 중요도

| 중요도 | 의미 | PPT 작성 원칙 |
|---|---|---|
| **상** | 장애를 인지하지 못하거나 중요 알림이 전달되지 않을 수 있는 항목 | 반드시 점검하고 개선 필요 시 개별 슬라이드 작성 |
| **중** | 성능 저하, 운영 지연, 비용 증가 또는 반복 장애로 이어질 수 있는 항목 | 영향이 확인되면 개별 또는 영역별 슬라이드 작성 |
| **하** | 표준화, 자동화, 가시성 및 운영 편의 개선 항목 | 개선 로드맵 또는 종합 권고 슬라이드에 포함 |

### 결과

| 결과 | 판정 기준 |
|---|---|
| **정상** | 구성과 데이터가 존재하고 실제 운영 기준을 충족함 |
| **개선 필요** | 구성 누락, 데이터 공백, 잘못된 대상, 기준 미달 또는 운영 위험이 확인됨 |
| **해당 없음** | 대상 환경에서 해당 기능이나 리소스를 사용하지 않음 |

### 근거 유형

중요도(상/중/하)와 정상 기준은 Microsoft가 정한 값이 아니라 본 점검의 판단 기준입니다. 각 항목이 Microsoft 문서에 어느 정도 근거하는지는 7장에 다음 유형으로 표시합니다.

| 유형 | 의미 |
|---|---|
| **직접** | Microsoft 문서에 같은 내용의 권고가 있음 |
| **간접** | Microsoft 문서의 기능 설명이나 원칙에서 도출한 점검 항목 |
| **자체** | Microsoft 문서에 직접 근거가 없는 운영 점검 항목. 모범 사례로 표기하지 않음 |

## 3. 상세 점검 항목

### 3.1 서비스 및 리소스 상태

| ID | 중요도 | 확인 항목 | 어디서 확인 | 정상 기준 | 개선 필요 판단 | PPT 근거 |
|---|---|---|---|---|---|---|
| SH-01 | **상** | 현재 Azure 서비스 장애 | `Azure Portal > Monitor > Service Health > Service issues` | 대상 구독과 리전에 영향이 있는 활성 이슈가 없음 | 활성 이슈가 핵심 서비스 또는 리전에 영향을 줌 | 이슈 목록, 영향받는 구독·리전·서비스 |
| SH-02 | **상** | 계획된 유지보수 | `Monitor > Service Health > Planned maintenance` | 영향 범위와 일정이 파악되고 담당자에게 공유됨 | 핵심 서비스에 영향이 있으나 대응 계획이나 담당자가 없음 | 유지보수 일정과 영향 범위 |
| SH-03 | **상** | 핵심 리소스 상태 | `Monitor > Service Health > Resource Health` 또는 `리소스 > Resource health` | 핵심 리소스가 `Available` | `Unavailable`, `Degraded`, `Unknown` 상태가 존재함 | 상태 목록과 장애 이력 |
| SH-04 | **상** | Service Health 알림 | `Monitor > Alerts > Alert rules`에서 `Service Health` 신호 필터 | 구독 수준과 테넌트 수준 규칙이 대상 구독·리전·서비스를 포함하고 Action Group이 연결됨 | 규칙이 없거나, 테넌트 수준 이벤트를 놓치거나, 범위·수신자가 잘못됨 | Alert rule 범위와 Action Group |
| SH-05 | **중** | 최근 관리 작업 실패 | `Subscription > Activity log`, 기간 24시간/7일, Status=`Failed` | 반복되는 배포·권한·구성 실패가 없음 | 동일 작업이 반복 실패하거나 실패 원인이 미조치 상태임 | 실패 작업 목록과 상세 오류 |
| SH-06 | **중** | 신뢰성 권고 | `Azure Advisor > Reliability` | 핵심 리소스에 미조치 고위험 권고가 없음 | 고가용성, 영역 중복 또는 복구 관련 권고가 미조치 상태임 | Advisor 권고 목록 |
| SH-07 | **상** | Resource Health 알림 | `Monitor > Alerts > Alert rules`에서 `Resource Health` 신호 필터 | 핵심 리소스 유형에 대한 규칙과 운영 그룹 Action Group이 있음 | 규칙이 없어 리소스 상태 변경을 포털에서만 확인함 | Alert rule 범위와 Action Group |

### 3.2 모니터링 대상과 데이터 수집

| ID | 중요도 | 확인 항목 | 어디서 확인 | 정상 기준 | 개선 필요 판단 | PPT 근거 |
|---|---|---|---|---|---|---|
| DC-01 | **상** | 핵심 리소스 식별 및 담당자 | `Resource groups`, `Resource Graph Explorer`, 태그와 운영 문서 | 핵심 리소스, 업무 중요도, 담당자가 식별됨 | 중요 리소스가 목록에서 누락되거나 담당자를 확인할 수 없음 | 리소스 목록과 담당자/태그 현황 |
| DC-02 | **상** | 플랫폼 메트릭 수집 | `리소스 > Monitoring > Metrics` | 최근 24시간 데이터가 끊김 없이 조회됨 | 메트릭이 없거나 예상 트래픽 시간대에 데이터 공백이 있음 | Metrics 차트와 시간 범위 |
| DC-03 | **상** | Diagnostic settings 구성 | `리소스 > Monitoring > Diagnostic settings` | 운영·감사에 필요한 로그 카테고리가 활성화됨 | 설정이 없거나 필요한 로그가 선택되지 않음 | Diagnostic settings 상세 화면 |
| DC-04 | **상** | 로그 전송 대상 | `Diagnostic settings > Destination details` | 승인된 Log Analytics Workspace, Storage 또는 Event Hub로 전송됨 | 다른 환경의 Workspace로 전송되거나 대상이 유효하지 않음 | 대상 Workspace와 구독 정보 |
| DC-05 | **상** | 실제 로그 유입 | `Log Analytics workspace > Logs`에서 최근 30분/24시간 조회 | 설정한 리소스의 최신 로그가 조회됨 | 설정은 있으나 로그가 없거나 지속적인 수집 지연이 있음 | 마지막 로그 시간과 조회 결과 |
| DC-06 | **상** | VM Agent와 Heartbeat | `Monitor > Data Collection Rules`, `VM > Extensions`, Log Analytics `Heartbeat` | Azure Monitor Agent와 DCR이 연결되고 최근 Heartbeat가 존재함 | Agent 오류, DCR 미연결 또는 Heartbeat 중단이 있음 | DCR 대상, Agent 상태, 마지막 Heartbeat |
| DC-07 | **상** | 애플리케이션 텔레메트리 | `Application Insights > Overview, Transaction search, Failures` | Request, Dependency, Exception 데이터가 최신 상태로 수집됨 | 요청만 있고 종속성·예외 추적이 없거나 전체 텔레메트리가 중단됨 | Overview와 최근 Transaction |
| DC-08 | **중** | Activity Log 장기 보관 | `Monitor > Activity log > Export Activity Logs` | 감사 및 분석 요구 기간에 맞게 Workspace/Storage로 내보냄 | 기본 보존 기간만 사용하고 장기 감사 요구를 충족하지 못함 | Export 설정과 대상 |
| DC-09 | **중** | 중복 데이터 수집 | 리소스별 Diagnostic settings, DCR, Workspace 테이블 비교 | 같은 데이터가 불필요하게 여러 경로로 수집되지 않음 | 동일 로그가 여러 Workspace나 테이블로 중복 수집됨 | 중복 설정 비교 화면 |
| DC-10 | **중** | 테이블 보존 기간 | `Log Analytics workspace > Tables > Manage table` | 운영·감사 기준에 맞는 테이블별 보존 기간이 설정됨 | 모든 테이블이 동일 기간이거나 필수 로그 보존이 부족함 | 테이블별 Retention 설정 |
| DC-11 | **하** | 수집 구성 표준화 | `Azure Policy > Compliance`, 배포 템플릿 저장소 | 리소스 종류별 Diagnostic settings와 DCR 표준이 정의됨 | Portal에서 개별 설정하며 환경별 구성이 일관되지 않음 | Policy 준수율 또는 템플릿 목록 |
| DC-12 | **중** | Workspace 상태 모니터링 | `Log Analytics workspace > Insights`, Workspace health, `Alert rules` | 수집량, 응답 없는 로그 소스, 실패한 쿼리를 정기 확인하고 Workspace 상태 알림이 있음 | Workspace 문제를 알림 없이 사용자 보고로 인지함 | Workspace insights와 상태 알림 규칙 |
| DC-13 | **중** | Workspace 복원력 | `Log Analytics workspace > Overview`(리전), `Data export`, Workspace 복제 설정 | 데이터 복원력을 지원하는 리전에 있고 복구 요구에 맞는 보호(GRS/GZRS Storage로 Data export, 필요 시 복제)가 있음 | 복구 요구가 있으나 영역·리전 장애 대비책이 없음 | Workspace 리전, Data export 규칙, 복구 요구사항 |
| DC-14 | **중** | DCR 설계 | `Monitor > Data Collection Rules`, 규칙별 Data sources와 Transformation | DCR이 단일 목적이고 성능과 이벤트가 분리되며 Transformation이 단순함 | 한 DCR에 여러 목적이 섞이거나 복잡한 Transformation에 의존함 | DCR 목록과 Data sources 구성 |
| DC-15 | **중** | 레거시 에이전트 | `VM > Extensions`, Log Analytics `Heartbeat`의 `Category` | 모든 VM이 Azure Monitor Agent를 사용함 | Log Analytics Agent(MMA/OMS)가 남아 DCR별 필터링을 쓸 수 없음 | 에이전트 유형별 VM 목록 |

### 3.3 Alert rule과 알림 전달

| ID | 중요도 | 확인 항목 | 어디서 확인 | 정상 기준 | 개선 필요 판단 | PPT 근거 |
|---|---|---|---|---|---|---|
| AL-01 | **상** | 핵심 경고 범위 | `Monitor > Alerts > Alert rules` | 가용성, 오류, 지연, 포화 및 데이터 중단 경고가 핵심 서비스에 구성됨 | 중요 장애 시나리오를 탐지할 규칙이 없음 | 핵심 리소스와 Alert rule 매핑 |
| AL-02 | **상** | Alert rule 활성 상태 | `Monitor > Alerts > Alert rules`, Status/Enabled 필터 | 사용 중인 핵심 규칙이 활성 상태이고 평가 오류가 없음 | 비활성 규칙, 삭제된 리소스 참조 또는 평가 실패가 있음 | 비활성/오류 규칙 목록 |
| AL-03 | **상** | Action Group 수신자 | `Monitor > Alerts > Action groups` | 개인이 아닌 운영 그룹이 포함되고 연락처가 최신 상태임 | 퇴사자, 개인 계정 또는 확인되지 않은 Webhook만 등록됨 | Action Group 수신자와 작업 목록 |
| AL-04 | **상** | 실제 알림 전달 | `Action group > Test`와 수신 채널 | 테스트 알림이 Email, SMS, Teams/Webhook 또는 ITSM에 도착함 | 테스트 실패, 지연 또는 일부 채널 미수신 | 테스트 결과와 실제 수신 화면 |
| AL-05 | **상** | 최근 Fired alert 처리 | `Monitor > Alerts`, 기간 24시간/7일 | 중요 경고가 담당자에게 할당되고 원인·조치가 추적됨 | 반복 경고가 미확인 상태이거나 담당자와 조치 기록이 없음 | Fired alert 목록과 상세 이력 |
| AL-06 | **중** | 임계값 적정성 | `Alert rule > Condition`과 최근 Metrics/Logs | 업무 기준이나 정상 기준선에 맞는 임계값을 사용함 | 기본값을 그대로 사용하거나 정상 상황에서도 반복 발생함 | 조건과 실제 추세를 함께 캡처 |
| AL-07 | **중** | 평가 주기와 집계 기간 | `Alert rule > Condition > Check every / Lookback period` | 탐지 목표시간과 데이터 발생 주기에 맞게 설정됨 | 평가가 너무 느리거나 일시적 변동마다 경고가 발생함 | 평가 주기와 집계 기간 |
| AL-08 | **중** | 중복 및 경고 폭주 | `Monitor > Alerts`에서 Alert rule별 발생 건수 비교 | 동일 원인으로 과도한 중복 알림이 발생하지 않음 | 단일 장애로 다수의 유사 경고가 반복됨 | 규칙별 발생 건수와 시간대 |
| AL-09 | **중** | 유지보수 억제 | `Monitor > Alerts > Alert processing rules` | 계획된 작업 중 필요한 범위만 일시 억제함 | 유지보수마다 수동 비활성화하거나 광범위하게 장기 억제함 | Processing rule 범위와 일정 |
| AL-10 | **중** | 대응 절차 연결 | Alert rule 설명, ITSM, Wiki 또는 Runbook | 중요 경고에 담당자와 초기 대응 절차가 연결됨 | 알림은 오지만 다음 조치와 담당자를 알 수 없음 | Alert 설명과 Runbook 링크 |
| AL-11 | **하** | 경고 구성 코드화 | Bicep, ARM, Terraform 또는 배포 파이프라인 | 중요 규칙과 Action Group이 코드로 재현 가능함 | Portal에만 존재하여 변경 이력과 재배포가 어려움 | 저장소와 배포 이력 |
| AL-12 | **상** | 권장 경고 대비 커버리지 | AMBA 경고 목록(Excel)과 `Monitor > Alerts > Alert rules` 비교 | 핵심 리소스 유형별 AMBA 권장 경고 또는 동등한 규칙이 구성됨 | 핵심 리소스 유형에 권장 경고가 다수 누락됨 | 리소스 유형별 권장 경고 대비 구성 현황 |
| AL-13 | **중** | 규칙 확장성 | `Alert rules`의 Scope, `Alert processing rules` | 동일 유형 리소스는 다중 리소스 규칙으로 관리하고 대규모 동작은 Processing rule로 제어함 | 리소스마다 개별 규칙이 있어 변경과 누락 확인이 어려움 | 규칙 수와 Scope 구성 |
| AL-14 | **중** | 경고 보안 구성 | `Alert rule > Details`(Managed identity), Action group의 Webhook, `Access control (IAM)` | 로그 검색 규칙에 Managed identity를 쓰고, Secure webhook을 사용하며, 조회만 필요한 사용자는 Monitoring Reader임 | 규칙이 사용자 권한에 의존하거나 일반 Webhook만 쓰고 광범위한 권한을 부여함 | Identity, Webhook 유형, 역할 할당 |
| AL-15 | **하** | 대량 알림 한도 | `Action groups`의 알림 유형, 규칙별 발생 건수 | 대량 알림이 예상되면 Webhook·Logic Apps 같은 프로그램 방식을 사용함 | 이메일/SMS 서비스 한도에 도달할 수 있는 구성임 | Action Group 작업 유형과 최대 발생량 |

> Action Group 테스트는 실제 수신자에게 메시지를 보낼 수 있으므로 담당자와 사전 협의 후 수행합니다.

### 3.4 가용성, 오류, 지연 및 리소스 포화

| ID | 중요도 | 확인 항목 | 어디서 확인 | 정상 기준 | 개선 필요 판단 | PPT 근거 |
|---|---|---|---|---|---|---|
| PF-01 | **상** | 서비스 가용성 | `Application Insights > Availability` 또는 외부 상태 점검 | 핵심 사용자 경로를 주기적으로 검사하고 목표 가용성을 충족함 | Availability test가 없거나 반복 실패가 있음 | 가용성 추세와 실패 위치 |
| PF-02 | **상** | 요청 실패율 | `Application Insights > Failures`, Logs의 `AppRequests` | 허용 실패율 이내이며 급격한 증가가 없음 | HTTP 5xx, 실패 요청 또는 예외가 지속 증가함 | 실패율 추세와 상위 Operation |
| PF-03 | **상** | 응답시간 | `Application Insights > Performance` 또는 `Metrics` | 평균 및 P95/P99가 서비스 목표를 충족함 | 지연시간이 목표를 초과하거나 특정 API에서 집중됨 | 응답시간 추세와 느린 Operation |
| PF-04 | **상** | 데이터 수집 중단 탐지 | Log Analytics `Heartbeat`, 주요 테이블의 마지막 `TimeGenerated`, 관련 Alert rule | 수집 중단을 자동으로 탐지하고 알림 | 로그가 중단되어도 알림 규칙이 없음 | 마지막 수집 시간과 Alert rule |
| PF-05 | **중** | CPU와 메모리 포화 | `리소스 > Metrics`, `VM Insights > Performance` | 지속적인 고사용률이 없고 증가 추세에 여유가 있음 | 장시간 임계치 초과 또는 반복적인 메모리 부족이 있음 | 24시간/7일 비교 차트 |
| PF-06 | **중** | 디스크와 스토리지 | `VM Insights`, Storage/Database `Metrics` | 여유 공간, IOPS, 지연시간이 운영 기준 이내임 | 디스크 부족, Throttling 또는 높은 지연이 발생함 | 사용량·IOPS·Latency 차트 |
| PF-07 | **중** | 네트워크 및 연결 | 리소스 `Metrics`, `Network Watcher`, Load Balancer/Application Gateway 진단 | 연결 실패, SNAT/포트 고갈, 비정상 백엔드가 없음 | 연결 오류, 패킷 손실 또는 비정상 Backend가 반복됨 | 연결/Backend health 화면 |
| PF-08 | **중** | 종속 서비스 오류 | `Application Insights > Application map > Investigate failures` | 종속 호출 성공률과 응답시간이 기준 내에 있음 | 특정 DB, API 또는 외부 서비스에서 오류와 지연이 집중됨 | Application Map과 Dependency 상세 |
| PF-09 | **중** | 배포 전후 변화 | `Application Insights > Releases/Failures`, Activity Log, 배포 시스템 | 성능 저하 시점과 배포 변경을 연관 분석할 수 있음 | 배포 직후 오류 증가가 있으나 변경 이력이 연결되지 않음 | 배포 시점과 성능 추세 |
| PF-10 | **하** | 공통 대시보드 | `Monitor > Workbooks` 또는 Azure Managed Grafana | 서비스 담당자가 동일한 핵심 지표를 조회할 수 있음 | 지표가 여러 화면에 흩어져 장애 판단이 지연됨 | Workbook/Grafana 전체 화면 |
| PF-11 | **하** | 쿼리 성능과 감사 | `Workspace insights > Query audit`, `LAQueryLogs` | 쿼리 감사를 사용하고 비효율 쿼리와 장기간 조회를 Summary rule 등으로 개선함 | 느리거나 반복되는 장기 범위 쿼리가 대시보드·알림에 사용됨 | 비효율 쿼리 목록 |
| PF-12 | **하** | 서비스 한도와 수집 지연 | Azure Monitor service limits 문서, 로그의 `ingestion_time()`과 `TimeGenerated` 비교 | 한도와 정상 수집 지연을 알고 설계와 알림 기준에 반영함 | 한도 초과나 수집 지연으로 경고가 누락되거나 늦게 발생함 | 지연 측정 결과와 한도 검토 |

### 3.5 Log Analytics 사용량과 비용

| ID | 중요도 | 확인 항목 | 어디서 확인 | 정상 기준 | 개선 필요 판단 | PPT 근거 |
|---|---|---|---|---|---|---|
| CO-01 | **상** | Daily cap과 수집 중단 위험 | `Log Analytics workspace > Usage and estimated costs > Daily cap` | Cap이 정상 일 수집량보다 높고, 도달 알림과 사용률 알림(예: 90%)이 있음 | Cap이 낮아 중요 로그가 유실되거나 도달 전후 알림이 없음 | Daily cap 설정과 사용량 |
| CO-02 | **중** | 일별 데이터 수집량 | `Usage and estimated costs`, Logs의 `Usage` 테이블 | 최근 7일 수집량이 예상 범위 내에 있음 | 특정 시점부터 원인 불명의 수집량 급증이 있음 | 일별 수집량 추세 |
| CO-03 | **중** | 고비용 테이블 | Logs의 `Usage` 테이블, 테이블별 Usage 조회 | 고수집 테이블의 목적과 소유자가 명확함 | 사용하지 않는 데이터가 비용 대부분을 차지함 | 테이블별 GB 순위 |
| CO-04 | **중** | 로그 카테고리 적정성 | 리소스 `Diagnostic settings`와 실제 조회 빈도 | 장애·감사에 필요한 카테고리만 수집함 | 사용하지 않는 Verbose 로그 또는 중복 카테고리를 계속 수집함 | 카테고리 설정과 사용량 비교 |
| CO-05 | **중** | 보존 및 Archive 정책 | `Log Analytics workspace > Tables` | 테이블 중요도에 맞게 Analytics/Basic/Auxiliary 및 보존을 구분함 | 모든 데이터를 고비용 계층과 동일 보존 기간으로 유지함 | 테이블 플랜과 Retention |
| CO-06 | **중** | 비용 추세와 예산 | `Cost Management + Billing > Cost analysis/Budgets`, Service name=`Azure Monitor` | 월별 추세를 확인하고 예산 또는 비용 알림이 있음 | 비용 증가를 월말 청구서에서만 확인함 | Azure Monitor 비용 추세 |
| CO-07 | **하** | 비용 최적화 권고 | `Azure Advisor > Cost`와 Azure Monitor 비용 문서 | 권고를 검토하고 적용 여부와 사유를 기록함 | 권고가 장기간 미검토 상태임 | Advisor Cost 권고 |
| CO-08 | **중** | 가격 계층 | `Log Analytics workspace > Usage and estimated costs` | 일 수집량에 맞는 Pay-as-you-go 또는 Commitment tier를 사용하고, 여러 Workspace는 Dedicated cluster를 검토함 | 수집량이 충분한데 Pay-as-you-go만 사용함 | 계층별 예상 비용 비교 |
| CO-09 | **중** | 수집량 급증 알림 | `Alert rules`(Usage 기반 로그 검색 규칙), `Usage` 테이블 | 일 수집량 급증을 알리는 규칙이 있음 | 수집량 증가를 청구 시점에만 인지함 | 규칙 조건과 수집량 추세 |
| CO-10 | **중** | Application Insights 비용 | `Application Insights > Usage and estimated costs`, 샘플링 설정 | Workspace 기반이며 샘플링, 로그 레벨, 불필요한 수집이 조정됨 | 샘플링과 로그 레벨 조정이 없어 고비용 텔레메트리가 지속됨 | 데이터 유형별 수집량과 샘플링 설정 |
| CO-11 | **중** | VM 수집 최적화 | `Data Collection Rules`, `VM insights > Configuration` | 필요한 성능 카운터와 이벤트만 수집하고 사용하지 않는 Map·프로세스 수집을 끔 | 폴링 주기가 짧거나 사용하지 않는 데이터를 수집함 | DCR 수집 항목과 VM별 수집량 |
| CO-12 | **하** | 비운영 환경 설정 | 비운영 구독의 Diagnostic settings와 Retention | 비운영 환경의 로그 범위와 보존이 운영보다 낮게 설정됨 | 운영과 동일한 수집·보존으로 비용이 발생함 | 환경별 설정 비교 |

### 3.6 운영 거버넌스 및 보안

| ID | 중요도 | 확인 항목 | 어디서 확인 | 정상 기준 | 개선 필요 판단 | PPT 근거 |
|---|---|---|---|---|---|---|
| GV-01 | **중** | 모니터링 권한 | Subscription/Resource Group/Workspace `Access control (IAM)` | 최소 권한으로 운영 및 조회 역할을 분리함 | 광범위한 Owner/Contributor 권한에 의존함 | 역할과 할당 범위 |
| GV-02 | **중** | 모니터링 정책 준수 | `Azure Policy > Compliance` | Diagnostic settings, Agent, 보안 로그 정책의 준수율을 관리함 | 신규 리소스가 수동 설정에 의존해 누락됨 | Policy 준수율과 미준수 리소스 |
| GV-03 | **중** | 장애 대응 Runbook | Alert 설명, Automation Account, Wiki 또는 운영 문서 | 상 중요도 경고에 확인 순서, 담당자, 복구 절차가 있음 | 담당자 경험에 의존하고 표준 절차가 없음 | Runbook 또는 Automation 목록 |
| GV-04 | **중** | 점검 및 개선 이력 | ITSM, DevOps Board 또는 변경 관리 시스템 | 발견 항목에 담당자, 완료일과 상태가 기록됨 | 동일 문제가 반복되지만 조치 이력이 없음 | 개선 Backlog와 상태 |
| GV-05 | **하** | 구성 변경 이력 | Activity Log, Git 저장소와 배포 파이프라인 | Alert, DCR, Workbook 변경을 추적할 수 있음 | Portal 직접 변경으로 변경자와 변경 이유 추적이 어려움 | Git/배포 이력 또는 Activity Log |
| GV-06 | **하** | 정기 검토 주기 | 운영 캘린더 또는 작업 관리 시스템 | 월별/분기별로 임계값, 수신자, 비용과 미사용 규칙을 검토함 | 구축 이후 정기 검토가 없음 | 정기 점검 일정과 최근 결과 |
| GV-07 | **중** | Workspace 구조 | `Log Analytics workspaces` 목록과 설계 문서 | 운영/비운영, 필요 시 보안/운영 데이터를 분리하고 Workspace 수가 최소화됨 | 환경이 섞이거나 Workspace가 불필요하게 분산됨 | Workspace 목록과 용도 |
| GV-08 | **중** | 데이터 접근 제어 | Workspace `Properties`의 Access control mode, `Access control (IAM)`, 테이블 권한 | 모드가 `Use resource or workspace permissions`이고 민감 테이블은 테이블 수준 권한이나 Protected tables로 제한함 | Workspace 전체 권한이 광범위하게 부여됨 | 접근 모드와 테이블 권한 |
| GV-09 | **중** | 쿼리 감사 | Workspace `Diagnostic settings`의 `Audit` 카테고리, `LAQueryLogs` | 쿼리 감사 로그를 수집하고 접근을 제한하며 정기 검토함 | 누가 어떤 쿼리를 실행했는지 확인할 수 없음 | 감사 설정과 LAQueryLogs 조회 |
| GV-10 | **중** | 네트워크 격리 | Workspace `Network Isolation`, `Azure Monitor Private Link Scope` | 보안 요구에 따라 Private Link나 공용 네트워크 접근 제한이 적용됨 | 보안 요구가 있으나 공용 접근이 열려 있음 | 네트워크 접근 설정 |
| GV-11 | **상** | TLS 1.2 이상 | Resource Graph로 VM OS 점검, 에이전트와 API 클라이언트 | 로그 수집·조회 클라이언트가 TLS 1.2 이상을 지원함 | TLS 1.0/1.1에 의존하는 OS나 클라이언트가 있어 수집·조회가 실패할 수 있음 | 구형 OS와 클라이언트 목록 |
| GV-12 | **하** | 감사 데이터 변경 방지 | Workspace `Locks`, `Data export`, Storage 불변성 정책 | 감사 요건이 있는 로그는 불변 Storage로 내보내고 Workspace에 잠금이 있음 | 감사 요건이 있으나 삭제·변경 방지책이 없음 | 잠금, Export 규칙, 불변성 정책 |
| GV-13 | **중** | 민감 데이터 보호 | DCR Transformation, 로그 샘플 조회, Purge 이력 | 불필요한 민감 정보를 수집 전에 필터·변환하고 오수집 데이터의 Purge 절차가 있음 | 로그에 개인정보나 비밀 값이 그대로 저장됨 | Transformation 설정과 Purge 절차 |
| GV-14 | **하** | 암호화 키 관리 | `Log Analytics clusters`, Key Vault | 규제가 요구하면 Dedicated cluster와 고객 관리 키를 사용함 | 요구가 있으나 Microsoft 관리 키만 사용함 | 클러스터와 키 설정 |
| GV-15 | **하** | 프로그램 접근 인증 | Workspace API를 쓰는 앱, `Access control (IAM)` | API 접근에 Microsoft Entra ID를 사용하고 API 키는 쓰지 않음 | API 키로 조회하여 클라이언트별 감사가 어려움 | 인증 방식과 역할 할당 |

## 4. 2시간 점검 시 권장 순서

시간이 제한된 경우 다음 순서로 확인합니다.

1. **상 중요도 전체:** SH-01~04, SH-07, DC-01~07, AL-01~05, AL-12, PF-01~04, CO-01, GV-11
2. **중 중요도 중 현재 징후가 있는 영역:** Fired alert, 실패율, 지연, 리소스 포화, 수집량 급증
3. **중·하 개선 항목:** 정책, 코드화, 공통 대시보드, 정기 점검 체계

상 중요도 항목을 완료하지 못한 경우 미확인 항목 자체를 점검 제한사항으로 결과 자료에 표시합니다.

## 5. 증적 수집 기준

PPT에서 판단 근거를 명확히 하기 위해 화면을 캡처할 때 다음 정보를 포함합니다.

- Subscription 또는 대상 리소스 이름
- 조회 기간과 시간대
- 적용한 필터
- 정상 기준과 비교할 수 있는 수치 또는 상태
- Alert rule, Diagnostic settings 등 구성의 범위와 활성 상태

다음 정보는 캡처 전에 마스킹합니다.

- Access token, Client secret, Connection string
- 개인 전화번호와 불필요한 이메일 주소
- 외부 공유가 제한된 고객 식별정보

권장 파일명은 다음과 같습니다.

```text
<ID>_<resource>_<yyyymmdd>_<result>.png
```

예:

```text
AL-03_prod-action-group_20261026_improvement.png
```

## 6. PPT 작성 형식

### 6.1 전체 요약 슬라이드

| 영역 | 상 | 중 | 하 | 주요 이슈 |
|---|---:|---:|---:|---|
| 서비스 상태 | 0 | 0 | 0 |  |
| 데이터 수집 | 0 | 0 | 0 |  |
| Alert | 0 | 0 | 0 |  |
| 성능 및 가용성 | 0 | 0 | 0 |  |
| 비용 | 0 | 0 | 0 |  |
| 거버넌스 및 보안 | 0 | 0 | 0 |  |

숫자는 `개선 필요`로 판정된 항목 수를 기입합니다.

### 6.2 이슈 상세 슬라이드

예시 이미지와 같이 슬라이드를 `이슈 사항`과 `권고 사항`으로 구분하고 다음 내용을 사용합니다.

```text
제목: [영역] 핵심 이슈 요약
중요도: 상 / 중 / 하
대상: Subscription / Resource Group / Resource

이슈 사항
- 확인 결과와 현재 구성
- 정상 기준과의 차이
- 운영 또는 비용 영향

권고 사항
- 변경할 설정 또는 추가할 구성
- 기대 효과
- 권장 담당자와 완료 시점

근거
- Azure Portal 화면
- Metrics 또는 KQL 결과
```

### 6.3 결과 기록표

| ID | 중요도 | 결과 | 대상 리소스 | 확인 결과 | 이슈 사항 | 권고 사항 | MS 근거 | 증적 파일 | 담당자 | 완료 예정일 |
|---|---|---|---|---|---|---|---|---|---|---|
|  |  | 정상/개선 필요/해당 없음 |  |  |  |  | R1 등 7장 코드 |  |  |  |

## 7. Microsoft 모범 사례 근거 매핑

Microsoft 문서는 2026-10-27 기준으로 확인했습니다. 고객 제출 전에 문서 개정 여부를 다시 확인합니다.

### 7.1 근거 문서

| 코드 | 문서 |
|---|---|
| **R1** | [Azure Monitor Logs 모범 사례](https://learn.microsoft.com/ko-kr/azure/azure-monitor/logs/best-practices-logs) |
| **R2** | [Azure Monitor 경고 모범 사례](https://learn.microsoft.com/ko-kr/azure/azure-monitor/alerts/best-practices-alerts) |
| **R3** | [Azure Monitor 비용 최적화](https://learn.microsoft.com/ko-kr/azure/azure-monitor/fundamentals/best-practices-cost) |
| **R4** | [Well-Architected Framework: Log Analytics 아키텍처 모범 사례](https://learn.microsoft.com/ko-kr/azure/well-architected/service-guides/azure-log-analytics) |
| **R5** | [Azure Monitor Baseline Alerts(AMBA)](https://azure.github.io/azure-monitor-baseline-alerts/) |
| **R6** | [Azure Monitor로 Azure 리소스 모니터링](https://learn.microsoft.com/ko-kr/azure/azure-monitor/fundamentals/monitor-azure-resource) |

### 7.2 항목별 매핑

| ID | 근거 | 유형 | Microsoft 권고 요지 |
|---|---|---|---|
| SH-01~03, SH-05 | - | 자체 | Service Health, Resource Health, Activity log 활용 점검 |
| SH-04 | R2 | 직접 | 구독 수준과 테넌트 수준 Service Health 알림 규칙을 모두 구성 |
| SH-06 | R4 | 간접 | Azure Advisor 권고를 모범 사례 점검에 활용 |
| SH-07 | R2 | 직접 | Resource Health 알림 규칙으로 리소스 상태 변경을 실시간에 가깝게 통지 |
| DC-01 | - | 자체 | 핵심 리소스와 담당자 식별 |
| DC-02, DC-03, DC-05 | R6 | 간접 | 플랫폼 메트릭은 자동 수집되나 리소스 로그는 Diagnostic setting이 있어야 조회 가능 |
| DC-04 | R4 | 간접 | 수집 대상과 용도를 명확히 하는 Workspace 설계 |
| DC-06 | R3 | 간접 | Azure Monitor Agent와 DCR 사용 |
| DC-07 | R3 | 간접 | Workspace 기반 Application Insights 사용 |
| DC-08 | R4 | 간접 | 장기 보존이나 불변성이 필요한 데이터 유형을 구분 |
| DC-09 | R3 | 직접 | 에이전트 중복 구성 등으로 같은 데이터를 중복 수집하지 않음 |
| DC-10 | R1, R3, R4 | 직접 | 테이블 수준 보존과 장기 보존을 요구에 맞게 구성 |
| DC-11 | R4 | 직접 | IaC와 Azure Policy로 구성 표준 강제 |
| DC-12 | R1, R4 | 직접 | Workspace insights로 상태를 추적하고 Workspace 상태 알림 구성 |
| DC-13 | R1, R4 | 직접 | 데이터 복원력 리전 선택, GRS/GZRS로 Data export, 필요 시 Workspace 복제 |
| DC-14 | R4 | 직접 | 단일 책임 DCR, 성능과 이벤트 DCR 분리, Transformation 최소화 |
| DC-15 | R3 | 직접 | Log Analytics Agent에서 Azure Monitor Agent로 이전 |
| AL-01 | R5 | 간접 | 리소스 유형별 권장 경고 세트 기준으로 범위 점검 |
| AL-02~05 | - | 자체 | 규칙 상태, 수신자, 전달 테스트, 처리 이력 점검 |
| AL-06 | R2, R4 | 직접 | 동적 임계값 활용, 계절성·배포 패턴 검증 |
| AL-07 | R3 | 직접 | 로그 검색 규칙은 평가 주기가 짧을수록 비용 증가 |
| AL-08 | - | 자체 | 경고 폭주 점검 |
| AL-09 | R2 | 직접 | Alert processing rule로 대규모 동작 제어 |
| AL-10 | R2 | 직접 | Custom properties와 Logic Apps로 알림 보강과 연동 |
| AL-11 | R4 | 직접 | 경고와 진단 설정까지 IaC로 관리 |
| AL-12 | R5 | 직접 | AMBA 권장 경고 적용 |
| AL-13 | R2, R3 | 직접 | 다중 리소스 규칙 사용, 지나치게 넓은 메트릭 경고는 비용 증가 |
| AL-14 | R2 | 직접 | 로그 검색 규칙 Managed identity, Secure webhook, Monitoring Reader 최소 권한 |
| AL-15 | R2 | 직접 | 이메일/SMS 알림 서비스 한도 고려 |
| PF-01~03, PF-05~10 | - | 자체 | 서비스 가용성, 오류, 지연, 포화 신호 점검 |
| PF-04 | R4 | 간접 | 응답 없는 로그 소스와 수집 상태 추적 |
| PF-11 | R1, R4 | 직접 | 쿼리 감사, 비효율 쿼리 개선, Summary rule |
| PF-12 | R4 | 직접 | 서비스 한도와 수집 지연 이해 |
| CO-01 | R1, R3, R4 | 직접 | Daily cap 설정과 도달·사용률(예: 90%) 알림 |
| CO-02, CO-03 | R3, R4 | 직접 | 수집량 추세와 상위 테이블을 정기 분석 |
| CO-04 | R3, R4 | 직접 | 필요한 로그 카테고리만 수집하고 Transformation으로 필터 |
| CO-05 | R1, R3, R4 | 직접 | 디버깅·감사 테이블은 Basic plan, 보존과 Archive 구성 |
| CO-06 | R4 | 간접 | 비용 모델링과 증가 추세 파악 |
| CO-07 | R3 | 직접 | Advisor 비용 권고에 대한 알림 구성 |
| CO-08 | R3, R4 | 직접 | Commitment tier와 Dedicated cluster 검토 |
| CO-09 | R3, R4 | 직접 | 수집량 과다 알림 구성 |
| CO-10 | R3 | 직접 | Workspace 기반 전환, 샘플링, 로그 레벨 조정 |
| CO-11 | R3 | 직접 | 성능 카운터 폴링 감소, VM insights Map 수집 조정 |
| CO-12 | R4 | 직접 | 비운영 환경의 진단 설정과 보존을 별도로 검토 |
| GV-01 | R1 | 직접 | 범위별 기본 제공 역할로 최소 권한 부여 |
| GV-02 | R4 | 직접 | Azure Policy로 Workspace와 진단 설정 표준 강제 |
| GV-03, GV-04 | - | 자체 | 대응 절차와 개선 이력 관리 |
| GV-05 | R4 | 간접 | IaC와 버전 관리를 통한 변경 추적 |
| GV-06 | R4 | 직접 | 진단 설정, DCR, 로그 상세도를 주기적으로 재검토 |
| GV-07 | R3, R4 | 직접 | 최소 Workspace 수, 운영/비운영 분리, Sentinel 사용 시 비용 영향 고려 |
| GV-08 | R1, R4 | 직접 | `Use resource or workspace permissions`, 테이블 수준 RBAC, Protected tables |
| GV-09 | R1, R4 | 직접 | 쿼리 감사 구성과 정기 검토 |
| GV-10 | R1, R4 | 직접 | Private Link로 네트워크 격리 |
| GV-11 | R1 | 직접 | TLS 1.2 이상 필요(조회 API 2025-07-01, 수집 API 2026-03-01 시행) |
| GV-12 | R1, R4 | 직접 | Workspace 잠금과 불변 Storage로 Data export |
| GV-13 | R1, R4 | 직접 | 수집 전 필터·변환과 오수집 데이터 Purge |
| GV-14 | R1, R2, R4 | 직접 | Dedicated cluster의 고객 관리 키(요구 시) |
| GV-15 | R4 | 직접 | API 키 대신 Microsoft Entra ID 사용 |

`자체`로 표시된 항목은 고객 환경 운영 표준으로 제시하고 Microsoft 모범 사례라고 표현하지 않습니다.

## 8. 관련 문서

- [Azure Monitoring 2시간 점검 플랜](azure-monitoring-assessment-plan.md)
- [점검용 KQL](azure-monitoring-assessment-plan.md#5-점검용-kql)
- [Azure Monitoring 개요](README.md)
- [Azure Monitor 개요](https://learn.microsoft.com/ko-kr/azure/azure-monitor/fundamentals/overview)
- [Azure Service Health](https://learn.microsoft.com/ko-kr/azure/service-health/overview)
- [Diagnostic settings](https://learn.microsoft.com/ko-kr/azure/azure-monitor/data-collection/diagnostic-settings)
- [Azure Monitor Alerts](https://learn.microsoft.com/ko-kr/azure/azure-monitor/alerts/alerts-overview)
- [Log Analytics 개요](https://learn.microsoft.com/ko-kr/azure/azure-monitor/logs/log-analytics-overview)
- [Azure Monitor 비용 및 사용량](https://learn.microsoft.com/ko-kr/azure/azure-monitor/fundamentals/cost-usage)
