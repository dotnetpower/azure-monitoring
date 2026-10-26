# Azure Monitoring 2시간 점검 플랜

이 문서는 Azure 환경의 모니터링 구성과 운영 상태를 2시간 안에 빠르게 점검하기 위한 실무용 체크리스트입니다. 기존 문서의 [Azure Monitoring 개요](README.md)와 [Linux 사용자 정의 로그 수집 방법](text_log.md)은 상세 구성 시 참고합니다.

> **권장 범위:** 한 번의 점검에서는 구독 1개 또는 핵심 서비스 1개를 대상으로 합니다. 구독이나 서비스가 여러 개인 경우 업무 중요도가 높은 워크로드부터 반복 수행합니다.
>
> 점검 항목별 중요도, Azure Portal 경로, 판정 기준과 PPT 증적 수집 방법은 [Azure Monitoring 상세 점검 체크리스트](azure-monitoring-assessment-checklist.md)를 참고합니다.

## 1. 점검 목표와 산출물

점검 목표는 다음 네 가지입니다.

- 서비스 장애와 리소스 이상 상태를 확인합니다.
- 필요한 메트릭과 로그가 누락 없이 수집되는지 확인합니다.
- 중요한 이상 징후가 적절한 담당자에게 전달되는지 확인합니다.
- 성능, 안정성 및 모니터링 비용의 개선 항목을 도출합니다.

점검 완료 후 다음 산출물을 남깁니다.

- 점검 대상과 확인 기간
- 정상, 개선 필요, 긴급 조치 항목
- 화면 캡처 또는 KQL 쿼리 결과 등 판단 근거
- 조치 담당자와 완료 예정일

## 2. 사전 준비

점검 시작 전에 다음 정보를 준비합니다.

| 구분 | 준비 내용 |
|---|---|
| 대상 | Tenant, Subscription, Resource Group, 핵심 애플리케이션 |
| 기준 시간 | 최근 24시간과 최근 7일 |
| 담당자 | 서비스 담당자와 장애 알림 수신자 |
| 권한 | Reader, Monitoring Reader, Log Analytics Reader 또는 이에 준하는 권한 |
| 운영 기준 | 중요 서비스, 정상 트래픽 범위, 목표 응답시간, 허용 실패율 |

운영 기준이 없다면 이번 점검에서는 최근 7일의 평균과 최대값을 기준선으로 사용하고, 별도 개선 항목으로 기록합니다.

## 3. 120분 실행 계획

| 시간 | 점검 영역 | 주요 작업 | 완료 기준 |
|---:|---|---|---|
| 0~10분 | 범위 확정 | 대상 구독, 리소스 그룹, 핵심 서비스, 확인 기간을 기록 | 점검 대상과 담당자가 명확함 |
| 10~25분 | 서비스 상태 | Service Health, Resource Health, 계획된 유지보수, Activity Log 실패 확인 | 현재 Azure 장애와 리소스 이상 여부를 판단함 |
| 25~45분 | 데이터 수집 | 플랫폼 메트릭, Diagnostic settings, VM Insights, AMA/DCR, Log Analytics 수집 상태 확인 | 핵심 리소스의 메트릭과 로그 누락 여부를 판단함 |
| 45~70분 | 경고 체계 | Alert rule, Action Group, Service Health 경고, 최근 Fired alert 및 비활성 규칙 확인 | 중요 장애의 탐지 조건과 알림 수신자가 확인됨 |
| 70~95분 | 서비스 성능 | 요청량, 실패율, 응답시간, CPU, 메모리, 디스크, 네트워크 추세 확인 | 성능 이상과 용량 위험을 식별함 |
| 95~110분 | 로그 및 비용 | 테이블별 수집량, 보존 기간, 불필요한 로그, 수집 급증 확인 | 비용 증가 원인과 최적화 대상을 식별함 |
| 110~120분 | 결과 정리 | 이슈 우선순위, 근거, 조치, 담당자, 완료일 기록 | 실행 가능한 개선 목록이 완성됨 |

## 4. 단계별 점검 항목

### 4.1 범위 확정 - 0~10분

1. 점검 대상 Subscription과 Resource Group을 선택합니다.
2. 고객 또는 사용자가 직접 사용하는 핵심 서비스와 종속 리소스를 식별합니다.
3. 최근 24시간은 현재 상태 확인, 최근 7일은 추세 비교 기간으로 설정합니다.
4. 리소스 이름, 리전, 업무 중요도와 담당자를 기록합니다.

**확인 결과**

- [ ] 점검 대상 구독과 리소스 그룹이 기록되어 있음
- [ ] 핵심 서비스와 종속 리소스가 식별되어 있음
- [ ] 운영 담당자와 알림 수신자가 확인되어 있음

### 4.2 Service Health, Resource Health, Activity Log - 10~25분

Azure Portal에서 다음 순서로 확인합니다.

1. `Service Health`에서 현재 서비스 이슈, 보안 권고 및 계획된 유지보수를 확인합니다.
2. `Resource Health`에서 핵심 리소스가 `Available` 상태인지 확인합니다.
3. `Monitor > Activity Log`에서 최근 24시간의 `Failed` 작업을 확인합니다.
4. 서비스 장애 알림을 받을 Service Health Alert가 구성되어 있는지 확인합니다.

**개선 필요 조건**

- Azure 장애 또는 계획된 유지보수가 핵심 서비스에 영향을 줍니다.
- 핵심 리소스가 `Unavailable`, `Degraded` 또는 `Unknown` 상태입니다.
- 반복되는 배포, 권한 또는 구성 변경 실패가 있습니다.
- Service Health 알림이 없거나 담당자가 아닌 계정으로 전달됩니다.

### 4.3 메트릭 및 로그 수집 - 25~45분

핵심 리소스별로 다음 항목을 확인합니다.

1. Azure Monitor `Metrics`에서 최근 데이터가 조회되는지 확인합니다.
2. 리소스의 `Diagnostic settings`가 필요한 로그 카테고리를 수집하는지 확인합니다.
3. 로그 대상이 올바른 Log Analytics Workspace, Storage Account 또는 Event Hub인지 확인합니다.
4. VM은 VM Insights, Azure Monitor Agent와 Data Collection Rule 연결 상태를 확인합니다.
5. Log Analytics Workspace에서 최근 로그가 실제로 조회되는지 확인합니다.
6. 애플리케이션은 Application Insights 요청, 예외 및 종속성 데이터가 수집되는지 확인합니다.

모든 로그를 무조건 수집하지 않고 장애 분석, 감사, 보안 또는 운영 지표에 필요한 데이터만 선택합니다.

**개선 필요 조건**

- 핵심 리소스에 Diagnostic settings가 없습니다.
- 설정은 존재하지만 로그가 잘못된 Workspace로 전송됩니다.
- 최근 데이터가 없거나 수집 지연이 지속됩니다.
- VM Agent 또는 Data Collection Rule 연결이 끊어져 있습니다.
- 애플리케이션 요청과 예외 추적이 불가능합니다.

### 4.4 Alert rule과 Action Group - 45~70분

1. `Monitor > Alerts`에서 최근 24시간 및 7일의 Fired alert를 확인합니다.
2. 핵심 서비스에 가용성, 실패율, 지연시간 및 주요 리소스 포화 경고가 있는지 확인합니다.
3. 비활성화된 규칙, 오래된 리소스를 참조하는 규칙과 중복 규칙을 확인합니다.
4. Severity, 임계값, 집계 기간 및 평가 주기가 운영 기준과 맞는지 확인합니다.
5. Action Group의 Email, SMS, Teams/Webhook, ITSM 또는 자동화 대상이 유효한지 확인합니다.
6. 운영 영향이 없는 범위에서 Action Group 테스트 알림을 실행하고 수신 여부를 확인합니다.
7. 유지보수 시간에 사용할 Alert processing rule 또는 억제 기준이 있는지 확인합니다.

> 테스트 알림은 실제 담당자에게 전송될 수 있으므로 사전에 수신자에게 알린 후 실행합니다.

**필수 경고 예시**

| 영역 | 권장 신호 |
|---|---|
| 가용성 | Resource Health 이상, Availability test 실패 |
| 애플리케이션 | 요청 실패율, 예외 증가, 응답시간 증가 |
| 컴퓨팅 | CPU, 사용 가능 메모리, 디스크 여유 공간 |
| 데이터 | 로그 수집 중단, Heartbeat 누락 |
| 플랫폼 | Service Health와 계획된 유지보수 |

### 4.5 성능 및 애플리케이션 상태 - 70~95분

최근 24시간과 7일을 비교하여 다음 네 가지 신호를 확인합니다.

- **Traffic:** 요청 수, 처리량, 연결 수
- **Errors:** 실패율, HTTP 5xx, 예외, 종속성 실패
- **Latency:** 평균 및 P95/P99 응답시간
- **Saturation:** CPU, 메모리, 디스크, 연결 또는 스레드 한계

단일 순간의 최대값만으로 판단하지 않고 업무 시간, 배치 시간 및 배포 시점과 연관하여 확인합니다. Application Insights를 사용하는 경우 Application Map과 Failures 화면에서 장애가 시작된 구성 요소와 종속성을 확인합니다.

**개선 필요 조건**

- 실패율 또는 지연시간이 기준선을 지속적으로 초과합니다.
- 리소스 사용률이 장시간 임계치에 근접합니다.
- 특정 배포 이후 오류나 성능 저하가 증가했습니다.
- 핵심 사용자 경로를 확인할 Availability test가 없습니다.

### 4.6 Log Analytics 사용량 및 비용 - 95~110분

1. Log Analytics Workspace의 Usage and estimated costs를 확인합니다.
2. 최근 7일 동안 수집량이 큰 테이블과 갑자기 증가한 테이블을 식별합니다.
3. 테이블별 보존 기간이 운영 및 규정 요구사항과 맞는지 확인합니다.
4. 중복 데이터, 사용하지 않는 로그 카테고리와 과도하게 상세한 로그를 확인합니다.
5. 비용 절감을 위해 데이터 수집을 줄일 경우 장애 분석과 감사 요구사항에 미치는 영향을 먼저 확인합니다.

### 4.7 결과 정리 - 110~120분

발견 항목을 다음 기준으로 분류합니다.

| 우선순위 | 기준 | 목표 조치 시점 |
|---|---|---|
| P0 | 현재 장애, 모니터링 데이터 중단, 중요 경고 전달 실패 | 즉시 |
| P1 | 핵심 로그 또는 경고 누락, 높은 실패율이나 지속적인 성능 저하 | 1~3영업일 |
| P2 | 비용, 보존 기간, 중복 경고, 대시보드 및 운영 편의 개선 | 계획 반영 |

다음 표를 복사하여 최종 결과를 기록합니다.

| 우선순위 | 대상 리소스 | 발견 내용 | 근거 | 권장 조치 | 담당자 | 완료 예정일 | 상태 |
|---|---|---|---|---|---|---|---|
| P1 |  |  |  |  |  |  | Open |

## 5. 점검용 KQL

Workspace 구성과 수집 데이터에 따라 일부 테이블 또는 컬럼이 존재하지 않을 수 있습니다.

### 최근 실패한 Azure 관리 작업

```kusto
AzureActivity
| where TimeGenerated > ago(24h)
| where ActivityStatusValue =~ "Failure"
| project TimeGenerated, OperationNameValue, ResourceGroup, Caller
| order by TimeGenerated desc
```

### 최근 Heartbeat가 없는 서버

```kusto
Heartbeat
| where TimeGenerated > ago(24h)
| summarize LastHeartbeat = max(TimeGenerated) by Computer, _ResourceId
| extend MinutesSinceHeartbeat = datetime_diff("minute", now(), LastHeartbeat)
| where MinutesSinceHeartbeat > 10
| order by MinutesSinceHeartbeat desc
```

### 테이블별 최근 7일 수집량

```kusto
Usage
| where TimeGenerated > ago(7d) and IsBillable
| summarize GB = round(sum(Quantity) / 1000, 2) by DataType
| order by GB desc
```

### Application Insights 요청 품질

```kusto
AppRequests
| where TimeGenerated > ago(24h)
| summarize
    Requests = count(),
    Failures = countif(Success == false),
    FailureRate = round(100.0 * countif(Success == false) / count(), 2),
    P95Milliseconds = percentile(DurationMs, 95)
    by bin(TimeGenerated, 15m)
| order by TimeGenerated desc
```

## 6. Microsoft 공식 문서

- [Azure Monitor 개요](https://learn.microsoft.com/ko-kr/azure/azure-monitor/fundamentals/overview)
- [Azure Service Health](https://learn.microsoft.com/ko-kr/azure/service-health/overview)
- [Azure Resource Health](https://learn.microsoft.com/ko-kr/azure/service-health/resource-health-overview)
- [Azure Monitor Activity Log](https://learn.microsoft.com/ko-kr/azure/azure-monitor/fundamentals/activity-log)
- [Diagnostic settings](https://learn.microsoft.com/ko-kr/azure/azure-monitor/data-collection/diagnostic-settings)
- [Azure Monitor Alerts](https://learn.microsoft.com/ko-kr/azure/azure-monitor/alerts/alerts-overview)
- [Action Group](https://learn.microsoft.com/ko-kr/azure/azure-monitor/alerts/action-groups)
- [Log Analytics 개요](https://learn.microsoft.com/ko-kr/azure/azure-monitor/logs/log-analytics-overview)
- [Application Insights 개요](https://learn.microsoft.com/ko-kr/azure/azure-monitor/app/app-insights-overview)
- [Azure Monitor 비용 및 사용량](https://learn.microsoft.com/ko-kr/azure/azure-monitor/fundamentals/cost-usage)

## 7. 다음 점검으로 확장

2시간 점검에서 P0 또는 P1 항목이 발견되거나 다수의 구독을 운영하는 경우 다음 단계로 확장합니다.

- 리소스 유형별 표준 Diagnostic settings와 Alert rule 정의
- Azure Policy를 이용한 모니터링 구성 누락 탐지 및 자동 배포
- 서비스별 SLI, SLO와 오류 예산 정의
- 공통 Workbook 또는 Grafana 대시보드 구성
- 경고 대응 Runbook과 자동 복구 절차 작성
- 모니터링 구성의 Bicep, ARM 또는 Terraform 코드화
