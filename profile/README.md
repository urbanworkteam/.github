<div align="center">

<img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p01-cover.png" width="100%" alt="Farmily — 농장 이야기를 소비자에게 잇다"/>

# 🌾 Farmily

### 농민의 일상이 마케팅이 되는 순간 — AI 퍼스널 브랜딩 · 마케팅 자동화

**AWS Cloud School 13기 · Team Urbanwork**

![Platform](https://img.shields.io/badge/Platform-Amazon%20EKS%201.35-FF9900?style=flat-square&logo=amazoneks&logoColor=white)
![Region](https://img.shields.io/badge/Multi--Region-Seoul%20%7C%20Tokyo-232F3E?style=flat-square&logo=amazonaws&logoColor=white)
![DR](https://img.shields.io/badge/DR-Warm%20Standby%20·%20RTO%207m25s-2E7D32?style=flat-square)
![Migration](https://img.shields.io/badge/ECS→EKS-무중단%20·%205xx%200-4169E1?style=flat-square)

</div>

---

## 📖 프로젝트 소개

**Farmily**는 농민이 영농일지에 오늘 농사 내용을 기록하면, **AI가 채널에 맞는 문구와 카드뉴스를 자동으로 만들고 발행까지 대신**하는 마케팅 자동화 서비스입니다. 농장 프로필은 그대로 디지털 명함이 됩니다.

24시간 무중단 서비스를 목표로, 컨테이너 인프라를 **ECS에서 EKS로 무중단 전환**하고, 서울 리전 장애에 대비한 **도쿄 리전 웜 스탠바이 재해복구(DR)** 체계를 구축했습니다. 배포·확장·복구·관측을 오픈소스와 AWS 매니지드로 자동화했습니다.

<table>
  <tr>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p04-painpoints.png" alt="Pain Points"/></td>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p05-solution.png" alt="Solution"/></td>
  </tr>
</table>

---

## 👥 팀원

<img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p03-team.png" width="100%" alt="팀원 소개"/>

<table align="center">
  <tr>
    <td align="center"><a href="https://github.com/mins8578"><img src="https://github.com/mins8578.png" width="72" height="72" alt="mins8578"/><br/><sub><b>@mins8578</b></sub></a></td>
    <td align="center"><a href="https://github.com/Moriental"><img src="https://github.com/Moriental.png" width="72" height="72" alt="Moriental"/><br/><sub><b>@Moriental</b></sub></a></td>
    <td align="center"><a href="https://github.com/trieeun"><img src="https://github.com/trieeun.png" width="72" height="72" alt="trieeun"/><br/><sub><b>@trieeun</b></sub></a></td>
    <td align="center"><a href="https://github.com/eustass99"><img src="https://github.com/eustass99.png" width="72" height="72" alt="eustass99"/><br/><sub><b>@eustass99</b></sub></a></td>
    <td align="center"><a href="https://github.com/gimyw"><img src="https://github.com/gimyw.png" width="72" height="72" alt="gimyw"/><br/><sub><b>@gimyw</b></sub></a></td>
  </tr>
</table>

---

## 🗺️ 전체 아키텍처

컴퓨트를 **ECS에서 EKS로** 전환한 두 세대를 모두 운영했습니다. ECS는 Dev·Prod 이중 환경(Multi-AZ RDS·ElastiCache·알림 파이프라인)으로, EKS는 **서울 Active + 도쿄 Warm Standby 2-리전**에 Karpenter·KEDA·Istio·ArgoCD·Argo Rollouts를 얹어 구성했습니다.

**ECS 아키텍처 (Dev · Prod)**
<div align="center">
  <img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p19-ecs-architecture.png" width="100%" alt="ECS 아키텍처 (Dev·Prod)"/>
</div>

**EKS 아키텍처 (서울 Active · 도쿄 Warm Standby · DR 자동화)**
<div align="center">
  <img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/eks-dr-architecture-v3.png" width="100%" alt="EKS 2-리전 아키텍처 + DR 자동화 파이프라인"/>
</div>

---

## 🏗️ 서비스 아키텍처

소비자는 CloudFront·S3의 React SPA로 농장 명함을 조회하고, 요청은 ALB → EKS → Istio Ingress → `farmily-api` Pod → RDS로 흐릅니다. AI 콘텐츠 생성은 Strands Agent가 Bedrock LLM을 호출하고 Lambda가 카드뉴스를 렌더링해 S3에 저장합니다.

<table>
  <tr>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p08-arch-namecard.png" alt="소비자 명함 조회 Flow"/></td>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p10-arch-ai.png" alt="AI 콘텐츠 생성 Flow"/></td>
  </tr>
</table>

---

## 📦 온프레미스 → 클라우드 마이그레이션 (DMS)

온프레미스 VMware(CentOS 7 Web/WAS/DB 분리 서버)를 **7R 전략 중 Replatform**으로 ECS + RDS 관리형 아키텍처에 옮겼습니다. 소스·타깃이 모두 PostgreSQL이라 **AWS DMS 동종(Homogeneous) 마이그레이션**을 택해, Full Load + CDC로 **소스 무중단**을 유지하며 `pgvector` `vector(1024)` 같은 커스텀 타입까지 보존했습니다.

<table>
  <tr>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p14-migration-replatform.png" alt="7R Replatform"/></td>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p16-dms-architecture.png" alt="DMS 아키텍처"/></td>
  </tr>
</table>

**검증** — 전체 24개 테이블 · 약 540만 행을 오류 0으로 적재하고, 소스=타깃 행 수 일치 · **CDC 지연 0ms** · `vector(1024)` 타입·차원 보존을 확인했습니다.

<div align="center">
  <img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p17-dms-validation.png" width="80%" alt="데이터 정합성 · 스키마 검증"/>
</div>

---

## 🔄 ECS → EKS 무중단 전환

VPC·RDS·ElastiCache·S3는 그대로 두고 **컴퓨트만 EKS로 전환**하며 Prometheus·Grafana·ArgoCD를 내재화했습니다. 전환은 **ALB 가중 타깃그룹**으로 ECS(blue) ↔ EKS(green) 트래픽을 `100:0 → 90:10 → 50:50 → 0:100`으로 점진 이동 — **전 구간 5xx 0건**, 문제 시 가중치를 되돌려 **즉시 롤백**할 수 있습니다.

<table>
  <tr>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p24-ecs2eks-strategy.png" alt="ECS→EKS 전환 전략"/></td>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p25-bluegreen-cutover.png" alt="블루그린 컷오버"/></td>
  </tr>
</table>

---

## 🧪 운영 테스트 (부하 · 장애 검증)

**부하 테스트 — 온프레미스 vs ECS** · 같은 k6 스크립트를 단일 VM은 p95 44초에 마비됐지만, ECS Fargate는 태스크를 1→4로 자동 확장하며 427,421건을 **전 구간 에러 0%**로 흡수했습니다. 클라우드 전환의 값을 수치로 증명한 결과입니다.

<div align="center">
  <img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p32-loadtest-ecs.png" width="80%" alt="부하 테스트 — 온프레미스 vs ECS"/>
</div>

**장애 주입 (AWS FIS)** · Multi-AZ 환경이 실제 장애를 견디는지 강제 failover로 검증 — **DB RTO < 120초 · 데이터 무손실**.

<div align="center">
  <img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p28-fis-test.png" width="80%" alt="RDS FIS Failover Test"/>
</div>

---

## 🛡️ 재해복구 (DR)

**웜 스탠바이**를 선택했습니다 — 도쿄에 축소 복제본을 상시 가동하고, 장애 시 RDS 복제본을 승격·확장해 **분 단위로 복구**합니다. 백업·복원은 24시간 서비스엔 너무 느리고, 액티브-액티브는 비용 2배로 과했습니다.

Route 53 헬스체크가 서울 실패를 감지하면 도쿄로 전환하고, RDS PostgreSQL 18.3을 도쿄로 비동기 복제하다 장애 시 복제본을 주 DB로 승격합니다 — 목표 **RTO 10분 / RPO 1분**.

<table>
  <tr>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p33-dr-strategy.png" alt="DR 전략 — 웜 스탠바이"/></td>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p34-dr-architecture.png" alt="2-리전 DR 아키텍처"/></td>
  </tr>
</table>

복구는 **AWS Step Functions**가 6단계로 자동 진행합니다 — EventBridge 장애 감지 → Lambda 상태 진단 → **Bedrock AI 위험도 분석** → **Slack 사람 승인** → DB 승격·트래픽 전환 → Bedrock 결과 검증.

> **AI는 조언만 — 실제 복구 실행은 사람 승인 + 정해진 절차(Step Functions)가 합니다.**

<table>
  <tr>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p35-dr-pipeline.png" alt="DR 파이프라인"/></td>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p37-dr-failover-slack.png" alt="dr-brain Slack 알림"/></td>
  </tr>
</table>

**실제 장애를 발생시켜 복구 목표를 달성**했습니다 — 복구 시간 **7분 25초**(89%가 DB 승격 대기), 복제 지연 **약 4초**로 잃는 데이터는 4초 이내. 둘 다 목표 이내입니다.

<div align="center">
  <img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p36-dr-rto-rpo.png" width="80%" alt="DR 복구 목표 달성 — RTO 7m25s / RPO 4s"/>
</div>

---

## ⚙️ CI/CD · GitOps

GitHub Actions가 빌드·테스트(Gradle·SonarCloud) 후 ECR에 이미지를 한 번만 만들고, **ArgoCD가 GitOps로 pull**해 **Argo Rollouts 카나리**로 배포합니다. 인프라는 Jenkins·Terraform으로 관리합니다.

<div align="center">
  <img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p40-eks-cicd.png" width="90%" alt="EKS CI/CD 파이프라인"/>
</div>

---

## 📊 관측 (Observability)

kube-prometheus-stack이 클러스터 메트릭을, Fluent Bit·CloudWatch Agent가 로그·AWS 네이티브 메트릭을 수집해 AMG/AMP로 시각화하고, CW Alarm → SNS → Lambda → Slack으로 알립니다. Argo Rollouts·ALB·RDS·Pod를 실시간 대시보드로 봅니다.

<table>
  <tr>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p42-observability.png" alt="관측 · 알림 파이프라인"/></td>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p43-ops-dashboard.png" alt="운영 실시간 대시보드"/></td>
  </tr>
</table>

---

## 💰 비용 최적화 (FinOps)

월간 비용 리포트로 지출을 추적하고, **FinOps Agent**로 Prometheus 모니터링·FIS·RI/SP·Client VPN·CloudTrail을 조정해 **월 $107~157**를 절감했습니다.

<table>
  <tr>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p44-cost-monthly.png" alt="비용 최적화 Monthly Report"/></td>
    <td width="50%"><img src="https://raw.githubusercontent.com/urbanworkteam/.github/main/assets/p46-finops-agent.png" alt="FinOps Agent"/></td>
  </tr>
</table>

---

## ✨ 핵심 성과

| 항목 | 결과 |
|---|---|
| **온프레미스 → 클라우드 (DMS)** | 24개 테이블·약 540만 행 오류 0 적재, **CDC 지연 0ms**, pgvector 타입 보존 |
| **ECS → EKS 무중단 전환** | 전 구간(100:0 ~ 0:100) **5xx 오류 0건**, 데이터 손실 0, 즉시 롤백 |
| **재해복구 (DR)** | **RTO 7분 25초** · **RPO 4초** — 목표(10분/60초) 이내 달성 |
| **부하 내성** | ECS Fargate 태스크 1→4 자동 확장, 427,421건 무장애 · 에러 0% |
| **자동 배포** | ArgoCD GitOps + Argo Rollouts 카나리, Circuit Breaker 자동 롤백 |
| **자동 확장** | Karpenter(노드) + KEDA(파드) 이벤트 기반 오토스케일 |
| **보안 통신** | Istio STRICT mTLS 파드 간 자동 암호화, IRSA 최소 권한 |
| **비용 절감** | FinOps Agent로 월 $107~157 절감 |

---

## 📦 레포지토리

| 레포 | 설명 | 스택 |
|---|---|---|
| [`Frontend`](https://github.com/urbanworkteam/Frontend) | 모바일 앱 | TypeScript |
| [`Frontend-web`](https://github.com/urbanworkteam/Frontend-web) | 웹 (React SPA) | TypeScript |
| [`Backend`](https://github.com/urbanworkteam/Backend) | API 서버 (farmily-api) | Java · Spring Boot |
| [`AI`](https://github.com/urbanworkteam/AI) | AI 콘텐츠 생성 (Strands Agent) | Python |
| [`Infra`](https://github.com/urbanworkteam/Infra) | EKS·DR 인프라 (IaC) | Terraform (HCL) |

<div align="center">

**AWS Cloud School 13기 · Team Urbanwork** · 🌾 *Farmily*

</div>
