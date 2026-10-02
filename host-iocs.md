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
- curl 플래그 `-k`, `-s`, `--max-time 30` (C2/업로드 요청 시 사용) — verified (Microsoft, 2026-08-18; 2026-10-01 재페치 본문에서 직접 확인·승격)

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
  - **승격 (2026-10-01):** 위 curl 플래그(`-k`, `-s`, `--max-time 30`) 서술이 이번 실행의 Microsoft(2026-08-18) 원문 재페치 본문에서 "Notable URI Patterns & HTTP Details" 항목으로 직접 확인되어 verified로 승격 — 네트워크/C2 통신 패턴 섹션에 반영.
- InstallFix ↔ Claude Code 연관성 강화 — Trend Micro 보고서 제목("InstallFix and Claude Code: How Fake Install Pages Lead to Real Compromise")이 InstallFix 캠페인과 가짜 Claude Code 설치 페이지를 명시적으로 연결. 원문은 이번 실행에서도 차단되어 세부 IOC(특히 macOS측)는 미확인 — ⚠️ (미검증) (2026-09-21 추가)
- **주의 — AppleScript 가짜 시스템 프롬프트 계열 캠페인과의 혼동 가능성:** Netskope 등이 보고한 별개의 macOS ClickFix 캠페인은 AppleScript 대화상자로 가짜 시스템 암호 프롬프트를 반복 표시해 자격증명을 탈취하고, 14개 브라우저·16개 암호화폐 지갑·200개 이상 확장 프로그램에서 세션 쿠키를 수집한다고 서술됨(검색 스니펫, 원문 미확인). 가짜 Claude 설치 페이지·MacSync와의 연결점은 확인되지 않아 본 저장소 IOC로 미채택 — ⚠️ (미검증, 채택 보류) (2026-09-22 추가)
- Kaspersky 신버전 MacSync 보도 추가 확산 — IT-Online(2026-09-21)이 동일한 Kaspersky MacSync 신버전(인포스틸러+백도어) 보도를 재보도. 2026-09-17 항목 대비 새로운 기술적 세부사항 없음(원문 접근 차단) — ⚠️ (미검증) (2026-09-22 추가)
- LaaS(Loader-as-a-Service) 진화 서술 강화 — 검색 스니펫에 따르면 2026-02 시작된 세 번째 추적 캠페인이 네이티브 Mach-O 직접 전달 방식을 멀티스테이지 Loader-as-a-Service(LaaS) 모델로 대체 — 쉘 기반 로더, API 키 게이트형 C2, 동적 AppleScript 페이로드, 적극적인 인메모리 실행 사용. 기존 "파일리스/인메모리 실행 변종"(2026-09-17) 서술을 구체화하나 원문 미확인(출처 불명, MS-ISAC/CIS 계열 추정) — ⚠️ (미검증) (2026-09-23 추가)
- 피해자 규모 통계 (주의 — Windows/InstallFix 측 수치 가능성) — 검색 스니펫에 "15,600명 이상의 피해자 확인" 서술이 등장했으나, 동일 스니펫이 MSIX/HTA/PowerShell/AMSI 우회/mshta.exe 등 Windows 전용 기술 세부사항과 함께 기술되어 있어 macOS/MacSync가 아닌 InstallFix Windows 캠페인 통계일 가능성이 높음 — 본 저장소 범위(macOS)의 확정 수치로 채택하지 않음 — ⚠️ (미검증, 채택 보류) (2026-09-23 추가)
- Netskope AppleScript 계열 캠페인 재확인 — Netskope가 별도로 추적 중인 ClickFix→AMOS 캠페인(2026-07-28부터 추적, 감염된 WordPress 등 합법 웹사이트를 통한 가짜 "Bot Protection" 화면 유도, 12개 브라우저·200+ 확장에서 세션 쿠키 수집, 최근 스윕 감염률 37~39%)이 이번 실행 검색에서도 확인됨 — 2026-09-22 "혼동 가능성" 주의 항목과 동일 계열로 판단, 가짜 Claude 설치 페이지·MacSync 인프라와의 연결점은 여전히 미확인이라 IOC 미채택 — ⚠️ (미검증, 채택 보류) (2026-09-23 추가)
- 별도 Microsoft 리포트 확인 (비관련, 혼동 주의) — Microsoft가 2026-05-06 별도 게시한 "ClickFix campaign uses fake macOS utilities lures to deliver infostealers"는 가짜 macOS 유틸리티 위장 캠페인이며 가짜 Claude 설치 페이지를 다루지 않음 — 본 캠페인과 무관하여 IOC 미채택, 혼동 방지 목적으로만 기록 — (2026-09-23 추가)
- Kaspersky 신버전 MacSync 보도 추가 재보도 (pokde.net, brandiconimage.com) — 2026-09-17 항목과 동일한 Kaspersky 보도(인포스틸러+백도어 신버전)를 재보도하는 매체 추가 확인, 원문 모두 차단되어 신규 기술 세부사항 없음 — ⚠️ (미검증) (2026-09-24 추가)
- InstallFix ↔ Claude Code 연관성 재보도 (hendryadrian.com, blog.7ai.com) — Trend Micro "InstallFix and Claude Code" 보도 및 AI 개발자 도구 사칭 관련 서술을 재보도하는 매체 추가 확인, 원문 모두 차단되어 신규 기술 세부사항 없음 — ⚠️ (미검증) (2026-09-24 추가)
- MacSync 신버전 상세 — iCloud 캘린더 매개 전달 · Finder 위장 백도어 (Kaspersky 계열로 추정, 원문 전부 차단) — 신버전 MacSync가 AppleScript 기반에서 컴파일된 Objective-C/Swift 바이너리로 전환, 악성 DMG·바이너리 드로퍼 외에 iCloud 캘린더 이벤트·iCloud 파일 저장소를 매개로 페이로드를 전달한다는 서술(문서공유 앱·암호화폐 지갑 앱 등으로 위장한 최초 침투 파일 언급). 브라우저 데이터·암호화폐 지갑·Telegram 데이터·SSH/AWS/Kubernetes 설정·키체인 파일을 수집하며, Finder로 위장한 동반 백도어가 LaunchAgents, `.zshrc` 인젝션, Git hooks를 통해 지속성을 확보한다고 서술. MS-ISAC이 1,000건 이상의 IOC를 조기 공유했고 MDBR 서비스가 관련 DNS 요청 250만 건 이상을 차단했다는 통계도 언급됨. 가짜 Claude 설치 페이지·ClickFix와의 직접적 연결점은 이번 실행에서도 확인되지 않음(원문 blog.netmanageit.com, hendryadrian.com 신규글, radar.offseq.com, daily.dev, windowsforum.com 모두 프록시 차단) — ⚠️ (미검증) (2026-09-25 추가)
- Kaspersky 1차 출처 URL 특정 (securelist.com) 및 CIS SLTT 전용 게시글 확인 — 이번 실행에서 상기 "MacSync 신버전" 서술의 원출처로 추정되는 Kaspersky Securelist 원문(`securelist.com/macsync-new-version/121383/`)과 CIS(Center for Internet Security)의 "MacSync Stealer Campaign Impacting U.S. SLTT macOS Users" 게시글을 특정했으나 둘 다 프록시 차단으로 원문 미확보. helpnetsecurity.com·technadu.com·cryptika.com 등 재보도 매체도 동일 내용을 반복 확인(신규 기술 세부사항 없음). 기존 "SLTT 표적" 미검증 서술(2026-09-17)과 부합하나 여전히 원문 미확인 — ⚠️ (미검증) (2026-09-26 추가)
- 신규 재보도 매체 추가 확인 (techradar.com, hexnode.com) — TechRadar가 "MacSync 신버전"(iCloud 캘린더 매개 전달) 서술을, Hexnode가 "ClickFix macOS 크립토 드레이너/키체인 탈취" 서술을 각각 다루는 것으로 검색됨. 두 매체 모두 이번 실행에서 신규로 페치를 시도했으나 프록시 차단으로 원문 미확보. securelist.com·cisecurity.org·it-online.co.za도 재시도했으나 계속 차단 — 신규 기술 세부사항 없음 — ⚠️ (미검증) (2026-09-27 추가)
- 신규 후보 매체 추가 확인 (cyberpress.org, sophos.com, theregister.com, bitdefender.com) — Cyberpress가 "MacSync's New Infection Chain"(감염 체인 고도화)을, Sophos가 "Evil evolution: ClickFix and macOS infostealers"(ClickFix macOS 인포스틸러 전반)를, The Register(2026-04-21)가 "macOS ClickFix attacks deliver AppleScript stealers"를, Bitdefender가 "Windows and macOS Malware Spreads via Fake Claude Code Google Ads"(본 캠페인과 제목상 직접 연관된 1차 출처로 추정)를 각각 다루는 것으로 검색됨. 4건 모두 신규로 페치를 시도했으나 프록시 차단으로 원문 미확보. helpnetsecurity.com도 재시도했으나 계속 차단 — 신규 기술 세부사항 없음 — ⚠️ (미검증) (2026-09-28 추가)
- **Unit42 DMG 리포트 재확인 (2026-09-29):** `2026-06-20-ClickFix-campaign-delivers-macOS-infostealer-via-DMG.txt`를 재페치해 2026-09-19 정정 사항을 재검증 — 여전히 AMOS(Odyssey 변종) 전용이며 MacSync·가짜 Claude 설치 페이지 언급 없음. 판단 변경 없음(무관, IOC 미채택).
- **별도 Microsoft 리포트 전체 페치 확인 (2026-05-06, 2026-09-29):** 2026-09-23에 제목만 확인했던 "ClickFix campaign uses fake macOS utilities lures to deliver infostealers"를 전체 페치. CleanMyMac/디스크 공간 분석기 등 가짜 유틸리티 위장 ClickFix로, 70개 이상의 로더·헬퍼 도메인, IP 8종, SHA-256 4건, `/tmp/helper`·`/tmp/shub_<ID>`·`~/.mainhelper`·`~/.agent`·`com.google.keystone.agent.plist` 등 LaunchAgent/스테이징 경로를 확인했으나, 원문이 "가짜 Claude 제품 사칭은 확인되지 않음"이라 명시하고 "claudecodedoc.squarespace[.]com" 도메인도 "우연한 명명이며 일반 ClickFix 안내 페이지, Claude 특정 유인 아님"이라 판단함 — 본 저장소 범위(가짜 Claude 설치 캠페인) 밖으로 확정, IOC 미채택.
- **Microsoft 1차 출처(2026-08-18) 데이터셋 안정성 재확인 (2026-09-29):** 재페치 결과 기존 검증 도메인 31건, URI 패턴(`/curl/`, `/dynamic?txd=`, `/gate?buildtxd=`), PUT/`--data-binary`, `api-key` 헤더, 업로드 파라미터(`upload_id`/`chunk_index`/`total_chunks`)와 100% 동일 — 신규 도메인/해시 없음. ThreatFox·Beelzebub·Malwarebytes(2건)·blog.7ai.com은 이번에도 차단.
- **Microsoft 1차 출처 3연속 안정성 재확인 (2026-09-30):** 재페치 결과 다시 한 번 검증 도메인 31건 및 행동 패턴 100% 동일 — 신규 없음. ThreatFox·Beelzebub 재차 차단. 신규 시도한 securelist.com(Kaspersky "MacSync 신버전" 원문으로 추정), gbhackers.com, blog.netmanageit.com, aviatrix.ai 모두 프록시 차단으로 원문 미확보.
- "Toria" 가짜 암호화폐 지갑 앱 배포 경로 — 검색 스니펫에 따르면 MacSync 신버전이 "Toria"라는 이름의 가짜 크립토 지갑 앱(자체 웹사이트 보유, X·Telegram에서 홍보)을 통해 유포되었다는 서술 존재(원문 미확인, 정확한 출처 도메인 불명) — 기존 "MacSync 신버전"(iCloud 캘린더 매개, 2026-09-25) 서술의 배포 벡터를 보강하나, 가짜 Claude 설치 페이지와의 연결점은 확인되지 않음 — ⚠️ (미검증) (2026-09-30 추가)
- **주의 — 가짜 OpenAI Codex 광고 캠페인과의 구분:** The Register(2026-08-25, theregister.com, 원문 차단)가 가짜 **OpenAI Codex** 설치 안내 광고를 통해 Mac 악성코드를 유포하는 별개 캠페인을 보도. ClickFix 유사 기법을 사용하나 사칭 대상이 Claude가 아닌 OpenAI이며, 본 캠페인(MacSync/가짜 Claude 설치 페이지)과의 직접적 연결 여부는 확인되지 않음 — 혼동 방지 목적의 맥락 정보로만 기록, IOC 미채택 (2026-09-30 추가)
- 공유된 Claude 대화(chat) 링크 유인 벡터 — macOS 특정 명시 소스 추가 확인 (Rescana, Zscaler) — Rescana의 보도 제목이 "Claude LLM Artifacts Exploited to Distribute **Mac** Infostealer Malware via ClickFix Attack Chain **Targeting macOS Users**"로 명시적으로 macOS를 특정하고, Zscaler가 별도로 "ClaudeFix: Shared Claude Chats Meet ClickFix"를 보도함을 확인 — 2026-09-20에 기록한 "공유된 Claude 대화 링크 유인 벡터"(당시 cybersecuritynews.com 단일 출처, 원문 차단) 서술을 두 개의 독립 매체가 추가로 보강하나, 두 원문(rescana.com, zscaler.com) 모두 이번 실행에서 프록시 차단되어 직접 확인은 안 됨 — ⚠️ (미검증, 다중 출처로 신뢰도 상승) (2026-10-01 추가)
- 서명된 변종 재보도 (cybersecuritynews.com) — "New MacSync Stealer Uses Signed macOS App to Evade Gatekeeper and Steal Data"라는 제목을 신규 확인, 2026-09-17에 기록한 "서명된 변종(코드사이닝 우회 변화)" 서술과 일치 — 원문은 차단 목록(cybersecuritynews.com)에 포함되어 직접 확인 안 됨, 신규 기술 세부사항 없음 — ⚠️ (미검증) (2026-10-01 추가)
- **주의 — 신규 후보 도메인 (다른 유인 경로, 범위 불확실):** WebSearch 합성 요약(복수의 차단된 1차 출처 — bleepingcomputer.com, securelist.com, netmanageit.com, aviatrix.ai, cybersecuritynews.com 등을 인용)에서 MacSync 신버전(iCloud 캘린더 매개, 2026-09-25 기록)의 구체적 배포 URL로 다음 후보가 언급됨: `toria[.]app`, `warpcast[.]asia/Toria.dmg`, `streamyard.appstore.com[.]mx/installer.sh`, `slack.apple03cloudstore[.]com/installer.sh`. 이들은 가짜 크립토 지갑(Toria)·가짜 StreamYard/Slack 설치 스크립트로, 가짜 **Claude** 설치 페이지와는 다른 유인(lure)이며, 어떤 원문에서도 가짜 Claude 설치 페이지와의 직접적 연결이 확인되지 않음 — 말웨어 패밀리(MacSync)는 동일하나 유인 경로가 본 캠페인 범위(가짜 Claude 설치 사칭)인지 불확실. 또한 출처가 특정 기사 본문 인용이 아닌 검색엔진 합성 요약이라 개별 도메인-출처 대응 관계도 불명확 — 매우 낮은 신뢰도의 미검증 후보로만 기록, `blocklists/unverified-domains.txt`에 참고용으로만 추가, 차단 금지 — ⚠️ (미검증, 범위/신뢰도 모두 불확실) (2026-10-01 추가)
- **Microsoft(2026-08-18) 원문 "Notable Gaps" 명시 확인 (2026-10-02):** 이번 재페치에서 원문이 해시·IP·ASN·지속성 메커니즘·가짜 Claude Desktop 배포 전용 도메인을 "제공하지 않는다"고 명시적으로 밝히는 것을 확인 — 즉 Microsoft 출처(검증 도메인 31건, URI/curl 패턴)는 MacSync의 C2 행위 피벗만 다루며, 가짜 Claude 설치 페이지와의 연결 고리는 Rescana/Zscaler/SOC Prime/Rapid7 등 별도(모두 차단된) 출처에 의존함 — 기존 verified 항목의 출처 범위를 명확화, 신규 IOC 아님.
- jacksonvillemma[.]com · `/curl/<sha256>` URL 패턴 · Stage 2 zsh 로더 해시 — WebSearch 합성 요약(MS-ISAC/RST Cloud 계열로 추정, 원문 미확인)에서 "MacSync Stealer macOS IOC" 관련 신규 후보로 도메인 `jacksonvillemma[.]com`과 URL `http://jacksonvillemma[.]com/curl/7980485fb1e0b1b1d6307a92b5750c7055bc53b662005cbaa662ac634984363d`, 그리고 Stage 2 zsh 로더의 MD5 `277acd8e241c1341852ebcd401203ecc` · SHA-1 `7d28507870e94b809f25883dc9dae838549ddfac` · SHA-256 `2728a7d444cd65550f652a8c66eaced0fe6d0389161f86393ab73da8f446a362`가 확인됨. URL 경로 `/curl/`가 Microsoft(2026-08-18) 검증 패턴(`/curl/`, `/dynamic?txd=`, `/gate?buildtxd=`)과 일치해 동일 MacSync 인프라 계열일 가능성이 높으나, 검색엔진 합성 요약만으로 확인되고 원문 1차 출처를 특정·페치하지 못해 미검증으로만 기록 — `blocklists/unverified-domains.txt`(도메인), `hashes/sha256.txt`(SHA-256, provenance=unverified)에 반영. MD5/SHA-1은 본 저장소 해시 파일 스키마(SHA-256 전용) 밖이라 본 문서에만 기록 — ⚠️ (미검증) (2026-10-02 추가)
- RST Cloud 신규 리포트 제목 확인 — "MacSync Stealer: C2 Infrastructure Rotation" (rstcloud.com) — 신규로 직접 페치를 시도했으나 프록시 차단, 신규 기술 세부사항 없음 — ⚠️ (미검증) (2026-10-02 추가)
- **주의 — "Macfinger" 명칭과의 혼동 가능성:** SANS ISC(isc.sans.edu)가 "A Closer Look at Malware From the Macfinger ClickFix Campaign"이라는 제목으로 별도 명칭("Macfinger")의 macOS ClickFix 캠페인을 다루는 것으로 검색됨 — 원문 미페치(예산 초과), MacSync·가짜 Claude 설치 페이지와의 연결 여부 불명 — 기존 Amatera/AMOS 오탐 사례와 유사한 캠페인명 혼동 위험이 있어 IOC 미채택, 혼동 방지 목적의 맥락 정보로만 기록 — ⚠️ (미검증, 채택 보류) (2026-10-02 추가)
- 실제 claude.ai 링크 재사용 서술 — popularai.org의 "Claude Code scams now use real claude.ai links. Stay safe" 제목이 검색됨(원문 미페치) — 사기 유인 체인 후반부에 정식 claude.ai 링크가 등장할 수 있다는 서술로 추정되며, 이는 정식 도메인이 공격자 인프라가 되는 것이 아니라 피싱 체인 내 정상 링크 삽입(신뢰도 가장)을 의미 — 기존 "anthropic.com/claude.ai는 결코 블록리스트에 포함하지 않는다" 원칙을 재확인시키는 맥락 정보로만 기록, IOC 미채택 — ⚠️ (미검증) (2026-10-02 추가)

## 대응 권고 (고정 — 자문 내용이 바뀔 때만 갱신)

- 감염 의심 단말은 즉시 네트워크에서 격리
- 브라우저 전체 종료 후 서버측 세션·토큰 강제 무효화 (비밀번호 변경/MFA 활성화만으로는 이미 탈취된 세션 쿠키가 무효화되지 않음)
- 비밀번호 재설정, 필요 시 OAuth 토큰·앱 비밀번호 폐기
- 최근 로그인 기록에서 비정상 IP·지역·기기 접속 여부 점검
- 상기 파일 경로(`.com.apple.airport`, `cksyncd`, `/tmp/sync*`, `/tmp/osalogging.zip`) 및 LaunchAgent/Daemon 존재 여부 EDR/수동 점검
- `xattr`, `codesign --force --sign -`, `open -g -j -n -a` 조합의 터미널 명령 실행 이력 로그 점검
