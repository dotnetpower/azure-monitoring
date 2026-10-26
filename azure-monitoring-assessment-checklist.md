# Azure Monitoring 상세 점검 체크리스트

이 문서는 [Azure Monitoring 2시간 점검 플랜](azure-monitoring-assessment-plan.md)을 수행하면서 무엇을 확인하고, Azure Portal의 어디에서 근거를 수집할지 정리한 문서입니다. 점검 결과를 `이슈 사항`, `권고 사항`, `근거 화면` 중심의 PowerPoint로 작성하는 것을 전제로 합니다.

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

## 3. 상세 점검 항목

### 3.1 서비스 및 리소스 상태

| ID | 중요도 | 확인 항목 | 어디서 확인 | 정상 기준 | 개선 필요 판단 | PPT 근거 |
|---|---|---|---|---|---|---|
| SH-01 | **상** | 현재 Azure 서비스 장애 | `Azure Portal > Monitor > Service Health > Service issues` | 대상 구독과 리전에 영향이 있는 활성 이슈가 없음 | 활성 이슈가 핵심 서비스 또는 리전에 영향을 줌 | 이슈 목록, 영향받는 구독·리전·서비스 |
| SH-02 | **상** | 계획된 유지보수 | `Monitor > Service Health > Planned maintenance` | 영향 범위와 일정이 파악되고 담당자에게 공유됨 | 핵심 서비스에 영향이 있으나 대응 계획이나 담당자가 없음 | 유지보수 일정과 영향 범위 |
| SH-03 | **상** | 핵심 리소스 상태 | `Monitor > Service Health > Resource Health` 또는 `리소스 > Resource health` | 핵심 리소스가 `Available` | `Unavailable`, `Degraded`, `Unknown` 상태가 존재함 | 상태 목록과 장애 이력 |
| SH-04 | **상** | Service Health 알림 | `Monitor > Alerts > Alert rules`에서 `Service Health` 신호 필터 | 대상 구독·리전·서비스를 포함한 활성 규칙과 Action Group이 있음 | 규칙이 없거나 범위·수신자가 잘못됨 | Alert rule 범위와 Action Group |
| SH-05 | **중** | 최근 관리 작업 실패 | `Subscription > Activity log`, 기간 24시간/7일, Status=`Failed` | 반복되는 배포·권한·구성 실패가 없음 | 동일 작업이 반복 실패하거나 실패 원인이 미조치 상태임 | 실패 작업 목록과 상세 오류 |
| SH-06 | **중** | 신뢰성 권고 | `Azure Advisor > Reliability` | 핵심 리소스에 미조치 고위험 권고가 없음 | 고가용성, 영역 중복 또는 복구 관련 권고가 미조치 상태임 | Advisor 권고 목록 |

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

### 3.5 Log Analytics 사용량과 비용

| ID | 중요도 | 확인 항목 | 어디서 확인 | 정상 기준 | 개선 필요 판단 | PPT 근거 |
|---|---|---|---|---|---|---|
| CO-01 | **상** | Daily cap과 수집 중단 위험 | `Log Analytics workspace > Usage and estimated costs > Daily cap` | Cap 도달 가능성을 모니터링하고 중단 위험을 인지함 | Cap 도달로 중요 로그가 유실되거나 알림이 없음 | Daily cap 설정과 사용량 |
| CO-02 | **중** | 일별 데이터 수집량 | `Usage and estimated costs`, Logs의 `Usage` 테이블 | 최근 7일 수집량이 예상 범위 내에 있음 | 특정 시점부터 원인 불명의 수집량 급증이 있음 | 일별 수집량 추세 |
| CO-03 | **중** | 고비용 테이블 | Logs의 `Usage` 테이블, 테이블별 Usage 조회 | 고수집 테이블의 목적과 소유자가 명확함 | 사용하지 않는 데이터가 비용 대부분을 차지함 | 테이블별 GB 순위 |
| CO-04 | **중** | 로그 카테고리 적정성 | 리소스 `Diagnostic settings`와 실제 조회 빈도 | 장애·감사에 필요한 카테고리만 수집함 | 사용하지 않는 Verbose 로그 또는 중복 카테고리를 계속 수집함 | 카테고리 설정과 사용량 비교 |
| CO-05 | **중** | 보존 및 Archive 정책 | `Log Analytics workspace > Tables` | 테이블 중요도에 맞게 Analytics/Basic/Auxiliary 및 보존을 구분함 | 모든 데이터를 고비용 계층과 동일 보존 기간으로 유지함 | 테이블 플랜과 Retention |
| CO-06 | **중** | 비용 추세와 예산 | `Cost Management + Billing > Cost analysis/Budgets`, Service name=`Azure Monitor` | 월별 추세를 확인하고 예산 또는 비용 알림이 있음 | 비용 증가를 월말 청구서에서만 확인함 | Azure Monitor 비용 추세 |
| CO-07 | **하** | 비용 최적화 권고 | `Azure Advisor > Cost`와 Azure Monitor 비용 문서 | 권고를 검토하고 적용 여부와 사유를 기록함 | 권고가 장기간 미검토 상태임 | Advisor Cost 권고 |

### 3.6 운영 거버넌스

| ID | 중요도 | 확인 항목 | 어디서 확인 | 정상 기준 | 개선 필요 판단 | PPT 근거 |
|---|---|---|---|---|---|---|
| GV-01 | **중** | 모니터링 권한 | Subscription/Resource Group/Workspace `Access control (IAM)` | 최소 권한으로 운영 및 조회 역할을 분리함 | 광범위한 Owner/Contributor 권한에 의존함 | 역할과 할당 범위 |
| GV-02 | **중** | 모니터링 정책 준수 | `Azure Policy > Compliance` | Diagnostic settings, Agent, 보안 로그 정책의 준수율을 관리함 | 신규 리소스가 수동 설정에 의존해 누락됨 | Policy 준수율과 미준수 리소스 |
| GV-03 | **중** | 장애 대응 Runbook | Alert 설명, Automation Account, Wiki 또는 운영 문서 | 상 중요도 경고에 확인 순서, 담당자, 복구 절차가 있음 | 담당자 경험에 의존하고 표준 절차가 없음 | Runbook 또는 Automation 목록 |
| GV-04 | **중** | 점검 및 개선 이력 | ITSM, DevOps Board 또는 변경 관리 시스템 | 발견 항목에 담당자, 완료일과 상태가 기록됨 | 동일 문제가 반복되지만 조치 이력이 없음 | 개선 Backlog와 상태 |
| GV-05 | **하** | 구성 변경 이력 | Activity Log, Git 저장소와 배포 파이프라인 | Alert, DCR, Workbook 변경을 추적할 수 있음 | Portal 직접 변경으로 변경자와 변경 이유 추적이 어려움 | Git/배포 이력 또는 Activity Log |
| GV-06 | **하** | 정기 검토 주기 | 운영 캘린더 또는 작업 관리 시스템 | 월별/분기별로 임계값, 수신자, 비용과 미사용 규칙을 검토함 | 구축 이후 정기 검토가 없음 | 정기 점검 일정과 최근 결과 |

## 4. 2시간 점검 시 권장 순서

시간이 제한된 경우 다음 순서로 확인합니다.

1. **상 중요도 전체:** SH-01~04, DC-01~07, AL-01~05, PF-01~04, CO-01
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
| 거버넌스 | 0 | 0 | 0 |  |

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

| ID | 중요도 | 결과 | 대상 리소스 | 확인 결과 | 이슈 사항 | 권고 사항 | 증적 파일 | 담당자 | 완료 예정일 |
|---|---|---|---|---|---|---|---|---|---|
|  |  | 정상/개선 필요/해당 없음 |  |  |  |  |  |  |  |

## 7. 관련 문서

- [Azure Monitoring 2시간 점검 플랜](azure-monitoring-assessment-plan.md)
- [점검용 KQL](azure-monitoring-assessment-plan.md#5-점검용-kql)
- [Azure Monitoring 개요](README.md)
- [Azure Monitor 개요](https://learn.microsoft.com/ko-kr/azure/azure-monitor/fundamentals/overview)
- [Azure Service Health](https://learn.microsoft.com/ko-kr/azure/service-health/overview)
- [Diagnostic settings](https://learn.microsoft.com/ko-kr/azure/azure-monitor/data-collection/diagnostic-settings)
- [Azure Monitor Alerts](https://learn.microsoft.com/ko-kr/azure/azure-monitor/alerts/alerts-overview)
- [Log Analytics 개요](https://learn.microsoft.com/ko-kr/azure/azure-monitor/logs/log-analytics-overview)
- [Azure Monitor 비용 및 사용량](https://learn.microsoft.com/ko-kr/azure/azure-monitor/fundamentals/cost-usage)
