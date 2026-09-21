# Host IOCs & 대응 권고 (누적)

이 문서는 누적 파일입니다. 실행 시마다 새 항목만 추가되며, 기존 항목은 삭제/강등되지 않습니다.

## 파일 경로

- `~/Library/Application Support/.com.apple.airport/` — 정상 macOS airport 관련 경로로 위장한 디렉터리 — seed
- `~/Library/Application Support/.com.apple.airport/cksyncd` — 지속성/동기화 목적 바이너리 — seed
- `~/Library/Application Support/.com.apple.airport/.cksyncd.v` — 숨김 버전/상태 파일 — seed
- `/tmp/sync*` — 스테이징/임시 파일 패턴 — verified (Microsoft, 2026-08-18)
- `/tmp/osalogging.zip` — 수집한 데이터를 압축해 스테이징하는 파일 — verified (Microsoft, 2026-08-18)

## 명령어 / 행위 패턴

- `xattr -rd com.apple.quarantine` — 격리(quarantine) 속성 제거로 Gatekeeper 우회 — seed
- `codesign --force --sign -` — 격리 속성 제거 후 애드혹(ad-hoc) 재서명 — seed
- `open -g -j -n -a --args` — 숨김/백그라운드 상태로 앱 실행 — seed
- `--user-data-dir=<...cksync...>` — cksync 명명된 프로파일 디렉터리를 지정해 Chromium을 실행, 브라우저 데이터(쿠키 등) 판독 — seed

## 네트워크 / C2 통신 패턴

- URI 패턴: `/curl/`, `/dynamic?txd=`, `/gate?buildtxd=` — verified (Microsoft, 2026-08-18)
- HTTP `PUT` 메서드 + `--data-binary` 사용 (exfil 업로드) — verified (Microsoft, 2026-08-18)
- 요청 헤더: `api-key`, macOS 계열 User-Agent 문자열 — verified (Microsoft, 2026-08-18)
- 업로드 파라미터: `upload_id`, `chunk_index`, `total_chunks` (청크 단위 분할 업로드) — verified (Microsoft, 2026-08-18)

## 미검증 항목

- API 키 `5190ef1733183a0dc63fb623357f56d6` — C2 인증(`api-key` 헤더) 값으로 추정. 언급한 원문(RST Cloud/Huntress 계열)이 프록시에 의해 차단되어 검색 스니펫으로만 확인됨 — ⚠️ (미검증)
- houstongaragedoorinstallers[.]com — 위 API 키와 함께 언급된 신규 C2 후보 도메인(RST Cloud, 검색 스니펫, 원문 차단) — ⚠️ (미검증) (2026-09-11 추가)
- MaaS(Malware-as-a-Service) 임대 모델 — MacSync Stealer가 타 공격자에게 임대되는 형태로 운영된다는 서술(RST Cloud 계열, 검색 스니펫, 원문 차단) — ⚠️ (미검증) (2026-09-17 추가)
- 파일리스/인메모리 실행 변종 — 2026-02 캠페인 변종이 파일리스·인메모리 실행 방식으로 EDR 탐지를 우회하며 미국 SLTT(주·지방·부족·준주) 정부기관을 표적했다는 서술(검색 스니펫, 원문 차단) — ⚠️ (미검증) (2026-09-17 추가)
- 6단계 감염 체인 · RAT 컴포넌트 — Huntress가 MacSync Stealer/RAT을 6단계로 리버스엔지니어링했다는 언급(제목만 확인, 원문 차단) — ⚠️ (미검증) (2026-09-17 추가)
- 서명된 변종(코드사이닝 우회 변화) — "재구성된 MacSync Stealer가 더 조용한 설치 방식(정식/도용 서명 인증서 사용 추정)을 채택"이라는 보도 제목(원문 미확인) — seed의 애드혹 코드사이닝(`codesign --force --sign -`)과 다른 우회 방식일 가능성 — ⚠️ (미검증) (2026-09-17 추가)
- SEO 포이즈닝 · 가짜 GitHub 저장소 배포 벡터 — ClickFix 붙여넣기 유도 외에 SEO 포이즈닝 및 가짜 GitHub 저장소를 통한 재유행이 보고되었다는 제목(daylight.ai, 원문 차단) — ⚠️ (미검증) (2026-09-17 추가)
- "InstallFix" 캠페인과의 연관 가능성 — 다수 벤더(Rapid7, Trend Micro, Malwarebytes, Push Security, Bitdefender 등)가 동일한 가짜 Claude Code/Claude Desktop 설치 페이지(Google 광고)가 Windows에는 MSIX/PowerShell 계열 악성코드를 배포한다고 보고. macOS/MacSync와 동일 배포 인프라를 공유하는 더 넓은 캠페인의 일부일 가능성이 있으나 Windows측 IOC는 본 저장소 범위 밖 — ⚠️ (미검증, 맥락 정보) (2026-09-17 추가)
- MacSync 신규 변종(백도어 겸용) — Kaspersky가 2026-09-17경 더 복잡한 감염 체인을 사용하는 MacSync 신버전을 발견, 인포스틸러와 백도어를 함께 전달하며 자격증명·사용자 데이터·암호화폐 자산을 표적한다고 보도(검색 스니펫, 원문/인용 매체 모두 원문 차단). 기존 Huntress "RAT 컴포넌트" 미검증 서술과 방향 일치 — ⚠️ (미검증) (2026-09-18 추가)
- InstallFix 플랫폼별 악성코드 배포 확인(부분) — Trend Micro가 InstallFix 캠페인이 Windows/macOS에 플랫폼별로 다른 악성코드를 배포한다고 명시(검색 스니펫, 원문 차단) — 기존 "연관 가능성" 서술을 강화하나 원문 미확인 상태 유지 — ⚠️ (미검증) (2026-09-18 추가)
- DMG 매개 배포 변형 — Google Ads 기반 ClickFix 캠페인이 DMG 파일을 통해 Mach-O를 전달하며 페이로드로 Macsync·Shub Stealer·AMOS 등을 언급하는 리포트 존재(Unit42 관련, 원문 미확인) — seed의 직접 다운로드형 Mach-O 외에 DMG 매개 변형 가능성 — ⚠️ (미검증) (2026-09-18 추가)
  - **정정 (2026-09-19):** 위 항목이 인용한 Unit42 GitHub IOC 리포지토리(`2026-06-20-ClickFix-campaign-delivers-macOS-infostealer-via-DMG.txt`)를 직접 페치해 확인한 결과, 해당 리포트는 **AMOS(Atomic macOS Stealer)의 Odyssey 변종**을 다루며 가짜 CAPTCHA 유인을 사용하고 **MacSync나 가짜 Claude 설치 페이지는 전혀 언급하지 않음**. 해당 리포트의 IOC(svs-verificationdate[.]beer, fewfwfwfwfwf[.]info, 178.16.52[.]101, 196.251.107[.]171, SHA-256 4건)는 본 캠페인과 무관한 별개 인시던트로 판단해 채택하지 않음. 위 "DMG 매개 배포 변형" 서술 자체는 다른 출처(검색 스니펫)에 기반하므로 미검증 상태 유지하되, 이 특정 소스는 반증됨.
- Squarespace 서브도메인 호스팅 — 가짜 Claude Code 설치 페이지 일부가 Squarespace 서브도메인에 정식 문서 페이지를 그대로 복제해 호스팅했다는 서술(Bitdefender 관련 검색 스니펫, 원문 미확인) — Mac 방문자는 난독화된 명령으로 Mach-O 백도어 수신 — ⚠️ (미검증) (2026-09-19 추가)
- Google Sites + iframe 호스팅 경로 — 일부 서술에 따르면 피해자가 먼저 Google Sites 페이지에 도달하고, 공격자 인프라 콘텐츠가 iframe으로 삽입되어 표시됨 — Squarespace 복제 방식과는 별개/추가 경로로 추정(Bitdefender 관련 검색 스니펫, 원문 차단) — ⚠️ (미검증) (2026-09-20 추가)
- 공유된 Claude 대화(chat) 링크 유인 벡터 — 검색광고 외에 공유된 Claude 대화 링크도 유인으로 사용되었다는 서술(cybersecuritynews.com, 원문 차단) — ⚠️ (미검증) (2026-09-20 추가)
- Universal Mach-O 바이너리 — 최종 페이로드가 Intel/Apple Silicon 겸용 유니버설 바이너리라는 서술 — seed의 "아키텍처에 맞는 Mach-O" 표현을 구체화 — ⚠️ (미검증) (2026-09-20 추가)
- **주의 — Amatera Stealer 혼동 가능성:** 일부 검색 결과가 "Amatera Stealer"(주로 Windows 대상으로 알려진 별개 패밀리)를 가짜 Claude 설치 페이지와 연결짓는 서술을 포함하나 원문 미확인. 2026-09-19의 Unit42/AMOS 오탐 사례와 유사한 캠페인 혼동 위험이 있어 본 저장소의 MacSync IOC로 채택하지 않음 — ⚠️ (미검증, 채택 보류) (2026-09-20 추가)
- 가짜 설치 안내 호스팅 플랫폼 확장 — Squarespace/Google Sites 외에도 Cloudflare Pages, Tencent EdgeOne 등 정상 호스팅 플랫폼이 가짜 설치 안내 페이지 게재에 악용되었다는 서술(검색 스니펫, 원문 미확인). 이들 플랫폼 자체는 정상 서비스이므로 차단 대상 아님 — ⚠️ (미검증) (2026-09-21 추가)
- 실행 파일명 패턴 — 2026-01 말 캠페인에서 "helper" 또는 "update"라는 이름의 실행 파일이 사용되었다는 서술(검색 스니펫, 원문 미확인) — ⚠️ (미검증) (2026-09-21 추가)
- curl 플래그 세부사항 — 이차 출처(검색 스니펫)에 따르면 Microsoft가 연결한 인프라의 curl 명령에 `-k`, `-s`, `--max-time` 플래그 사용이 언급됨. 금일 Microsoft 원문 재페치 결과에는 해당 플래그가 명시적으로 나타나지 않아 원문 직접 확인은 안 됨 — 기존 verified 항목(`--data-binary`, `PUT`)에 대한 보강 서술로만 기록 — ⚠️ (미검증) (2026-09-21 추가)
- InstallFix ↔ Claude Code 연관성 강화 — Trend Micro 보고서 제목("InstallFix and Claude Code: How Fake Install Pages Lead to Real Compromise")이 InstallFix 캠페인과 가짜 Claude Code 설치 페이지를 명시적으로 연결. 원문은 이번 실행에서도 차단되어 세부 IOC(특히 macOS측)는 미확인 — ⚠️ (미검증) (2026-09-21 추가)

## 대응 권고 (고정 — 자문 내용이 바뀔 때만 갱신)

- 감염 의심 단말은 즉시 네트워크에서 격리
- 브라우저 전체 종료 후 서버측 세션·토큰 강제 무효화 (비밀번호 변경/MFA 활성화만으로는 이미 탈취된 세션 쿠키가 무효화되지 않음)
- 비밀번호 재설정, 필요 시 OAuth 토큰·앱 비밀번호 폐기
- 최근 로그인 기록에서 비정상 IP·지역·기기 접속 여부 점검
- 상기 파일 경로(`.com.apple.airport`, `cksyncd`, `/tmp/sync*`, `/tmp/osalogging.zip`) 및 LaunchAgent/Daemon 존재 여부 EDR/수동 점검
- `xattr`, `codesign --force --sign -`, `open -g -j -n -a` 조합의 터미널 명령 실행 이력 로그 점검
