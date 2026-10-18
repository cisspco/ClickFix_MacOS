# MacSync Stealer (ClickFix macOS) Snapshot — 2026-10-18 UTC

## 수집 상태
- 지정 출처 접근성: Microsoft(2026-08-18) — fetched, ThreatFox — blocked, Beelzebub — blocked
- 추가 페치 성공: 없음
- 추가 페치 차단: cert.gov.az, www.trendmicro.com, securityarsenal.com, www.sophos.com, socprime.com (전부 DNS 조회 실패; 전부 기존에 이미 차단으로 확인된 도메인 재시도)
- seed: 호스트 IOC 다수 / 검증: 도메인 31 / 미검증: 도메인 14, IP 1, 해시 3

## 캠페인 개요
가짜 Claude Desktop/Claude Code 설치 페이지(검색광고)를 통한 ClickFix 기법으로 사용자가 터미널에 명령을 직접 붙여넣게 유도하고, 아키텍처에 맞는 악성 Mach-O를 다운로드·실행. MacSync 모듈이 Chromium계·Firefox·Safari 쿠키·프로파일을 수집해 외부로 유출. 탈취된 세션 쿠키로 기존 로그인 재사용 가능.

**금일 조사 메모:** Microsoft(2026-08-18) 원문을 재페치해 검증 도메인 31건 및 호스트/네트워크 패턴이 20회 연속 100% 동일함을 재확인했다. ThreatFox·Beelzebub은 20일 연속 DNS 차단. 신규로 시도한 cert.gov.az("Warning for Mac users: fake Claude AI application" — 제목상 유망한 1차 출처), www.trendmicro.com(InstallFix/Claude Code 원문, en_us 변형 URL), securityarsenal.com(MacSync 행동 피벗/C2 헌팅 가이드, 구체 URL 특정), www.sophos.com(Evil Evolution, en-us 변형 URL), socprime.com(회전 C2 인프라 영문 액티브 스레트 페이지) 총 5건 모두 DNS 조회 실패로 원문을 확보하지 못했다 — 전부 이전 실행에서 이미 차단으로 확인된 도메인의 재시도(신규 차단 도메인 없음). WebSearch에서는 cert.gov.az 관련 서술("MacSync Stealer v1.1.2, build tag 'claude1'", 드로퍼 SHA-256, 트로이목마화 Ledger Live SHA-256, C2 `oklahomawarehousing.com`, URL 패턴 `/dynamic`·`/gate`·`/curl`·`/ledger/live`, 가짜 설치 페이지 `sites.google.com`)과 Trend Micro InstallFix(Windows측 mshta.exe/AMSI 우회/예약 작업 지속성, 국가 연계 추정, Adception 변종으로 macOS 확장) 세부사항이 재확인됐으나, 전부 기존 `host-iocs.md`에 이미 기록된 미검증 항목과 일치해 신규 등재나 승격은 없었다. "cksyncd" 호스트 아티팩트는 이번 검색에서도 외부 보고서에서 전혀 확인되지 않아 seed 전용 상태를 유지한다 — 조용한 하루.

## 공격 체인
1. 가짜 설치 페이지 유도 (검색광고) — "claude mac" 등 검색 시 Google 스폰서 광고 노출. "Adception" 변종(⚠️ 미검증)은 표면 도메인으로 `bing.com`을 표시해 Bing 리디렉션 후 클로킹된 침해 WordPress 사이트를 경유, 가짜 다운로드 페이지 `claude-desk-code[.]com`(⚠️ 미검증)에 도달. 별개 변종으로 추정되는 `cladesktop.gitlab[.]io` / `cladesktop-apps[.]com`(⚠️ 미검증)도 가짜 Claude 다운로드 페이지로 식별됨. 추가로 공개 Claude 대화 artifact에 악성 명령을 직접 게시하거나 Medium 가짜 Apple 지원 페이지를 경유하는 별개 유인 경로, `sites.google.com/view/claud-version-0505` 공유 URL도 언급됨(⚠️ 미검증)
2. 터미널 명령 복사·붙여넣기 유도 — ClickFix 기법; 일부 변종은 화면 표시 명령과 클립보드 주입 명령이 다름(⚠️ 미검증) (T1204.004)
3. Mach-O 다운로드 · Gatekeeper 우회 — curl이 `lake-90[.]com`(⚠️ 미검증) 또는 `arkypc[.]com`(⚠️ 미검증)에서 페이로드 수신 후 zsh 파이프 실행; `xattr -rd com.apple.quarantine` 후 `codesign --force --sign -`로 애드혹 재서명. 일부 변종은 정식 서명·공증 샘플 사용(⚠️ 미검증 — Apple이 인증서 신고 후 해지)
4. 지속성 (cksyncd) — `~/Library/Application Support/.com.apple.airport/cksyncd` 등 위장 경로(seed, 외부 보고서 미확인 지속), `/tmp/sync*`·`/tmp/osalogging.zip` 스테이징(verified, Microsoft); 실행 시 Terminal 앱을 먼저 종료해 활동 은닉, XOR 난독화·프로세스 데몬화·포렌식 아티팩트 신속 제거(⚠️ 미검증)
5. 브라우저 정보 탈취 (MacSync) — `--user-data-dir=<...cksync...>` 프로파일로 Chromium 실행, Firefox/Safari 데이터도 수집; 신버전(v1.1.2, build "claude1"/"symbiot")은 가짜 비밀번호 프롬프트와 크립토 지갑 앱(Ledger Live 등) 트로이목마화도 포함 (⚠️ 미검증)
6. 외부 유출 · 세션 재사용 위험 — HTTP PUT + `--data-binary`, 청크 업로드(`upload_id`/`chunk_index`/`total_chunks`)로 C2에 전송; 경로 `/curl`, `/dynamic`, `/gate` (verified), `/ledger/live` (⚠️ 미검증). 정적 `api-key` 헤더 값이 4개 확인된 C2 도메인에서 공유됨(verified)

## 공격자 인프라 — 가짜 설치/C2 (차단 대상) (31)
- aihealthring[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- cabinrentalsnc[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- chatbasedos[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- commercialroofingsd[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- dogtrainersgeorgia[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- fintelliganceai[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- homeinspectionsdelaware[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- intopython[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- lalandscapelighting[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- lumenagnet[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- marbellaresales[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- miamipcsupport[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- moldinspectiondayton[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- nailscanai[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- newjerseypetsitter[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- numericagent[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- oaklandwaterdamage[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- oklahomawarehousing[.]com — C2/행동 피벗 식별 인프라 (검색 스니펫에서 "claude1" 빌드와 연계된다는 미검증 서술도 확인됨) — verified (Microsoft, 2026-08-18)
- olympiapetemergency[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- peaecagent[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- plasmaticsystems[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- plethorawallet[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- premierrentalpurchase[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- ricewaterbeauty[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- rvieragent[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- sandiegotkd[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- secueragent[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- shiledagent[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- syracusefertilitycenter[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- vastbets[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
- wvaeagent[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)

## 미검증 후보 인프라 (차단 금지) (14 도메인 / 1 IP)
- agenticsora[.]com — Stage 2 로더 후보 도메인 (Huntress 계열로 추정, 검색 합성 요약, 원문 미확인) — ⚠️ (미검증)
- arkypc[.]com — "cladesktop" 변종 Mach-O 페이로드 호스팅 후보 (Malware Traffic Analysis, 2026-04-22, 원문 미페치) — ⚠️ (미검증)
- claude-desk-code[.]com — "Adception" 변종 가짜 Claude 다운로드 페이지(화면 표시 명령과 클립보드 주입 명령이 다름) — 2차 매체 교차확인, 1차 출처 전부 차단 — ⚠️ (미검증)
- cladesktop-apps[.]com — URL 스캐너(malwaretips.com)가 Anthropic Claude AI 사칭·가짜 macOS/Windows/ChromeOS 다운로드로 플래그(탐지 데이터 오래됨), 원문 미페치 — ⚠️ (미검증, 범위/시점 불확실)
- cladesktop.gitlab[.]io — "cladesktop" 변종 가짜 Claude 다운로드 사이트 (Malware Traffic Analysis, 2026-04-22, 원문 미페치) — ⚠️ (미검증)
- focusgroovy[.]com — C2 후보, "추가 페이로드를 가져오는 C2 서버"로 언급 (Jamf로 추정, 원문 미확인) — ⚠️ (미검증)
- houstongaragedoorinstallers[.]com — C2 후보 (RST Cloud 계열, 검색 스니펫, 원문 차단) — ⚠️ (미검증)
- jacksonvillemma[.]com — C2 후보, URL `/curl/<sha256>` 패턴이 Microsoft 검증 URI 패턴과 일치 (MS-ISAC/RST Cloud 계열 추정, 검색 합성 요약, 원문 미특정) — ⚠️ (미검증)
- lake-90[.]com — "Adception" 변종에서 클립보드 주입 악성 명령이 `.dat` 페이로드를 받아오는 도메인(curl → zsh 파이프) — 2차 매체 교차확인, 1차 출처 전부 차단 — ⚠️ (미검증)
- malwareaudit[.]com — Stage 2 로더 후보 도메인 (Huntress 계열로 추정, 검색 합성 요약, 원문 미확인) — ⚠️ (미검증)
- slack.apple03cloudstore[.]com — 가짜 Slack 설치 스크립트 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- streamyard.appstore.com[.]mx — 가짜 StreamYard 설치 스크립트 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- toria[.]app — MacSync 신버전(가짜 크립토 지갑 "Toria") 배포 사이트 후보 — 가짜 Claude 설치 페이지와 무관 가능성, 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- warpcast[.]asia — Toria.dmg 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- **IP** 45.94.47[.]204 — "cladesktop" 변종 C2 후보 (Malware Traffic Analysis, 2026-04-22, 원문 미페치) — ⚠️ (미검증)

(참고: `sites.google.com/view/claud-version-0505`, 공개 Claude 대화 artifact·Medium 가짜 Apple 지원 페이지, 정식 `claude.ai` 공개 페이지상 게시된 악성 명령 사례, "Adception" 체인에서 사용된 클로킹된 침해 WordPress 사이트는 정상 서비스/도메인 위 콘텐츠 악용 또는 미특정이므로 도메인 자체를 어떤 블록리스트에도 등재하지 않음 — host-iocs.md 참조)

## 파일 해시 (3, 전부 미검증)
### SHA-256
- 1abf943e97356e07bde23663da544e7c106afc19827a2106361a52035737de43 — 트로이목마화된 Ledger Live 앱(크립토 지갑) — ⚠️ unverified (검색 합성 요약, 원문 미특정)
- 2728a7d444cd65550f652a8c66eaced0fe6d0389161f86393ab73da8f446a362 — Stage 2 zsh 로더(MacSync 추정) — ⚠️ unverified (검색 합성 요약, 원문 미특정)
- bd348a40261aa2d95566ccdc4e6f304ff25aa97d34e5c713c77c937583ad04f0 — MacSync Stealer v1.1.2 드로퍼 (build tag "claude1") — ⚠️ unverified (검색 합성 요약, 원문 미특정)

참고(본 저장소 스키마 밖, SHA-256 외 해시): MD5 `277acd8e241c1341852ebcd401203ecc`, SHA-1 `7d28507870e94b809f25883dc9dae838549ddfac` — Stage 2 zsh 로더와 동일 추정, `host-iocs.md`에만 기록.

## 호스트 IOC · 행위 (누적)
상세는 [`host-iocs.md`](../host-iocs.md) 참조 (경로/명령/네트워크 패턴/대응 권고 전체 누적 문서). 금일 신규: Microsoft 데이터셋 20일 연속 안정성 재확인. 그 외 신규 기술 세부사항 없음(조용한 하루).

## 이번 실행 변경사항
- 신규 인프라: 없음
- 신규 해시: 없음
- 승격(미검증→검증): 없음
- 폐기(sinkhole/takedown): 없음
- 신규 보고서: 없음 (신규 시도 5건 전부 차단; 모두 기존에 이미 차단으로 확인된 도메인)

## 대응 권고 (참고 — 고정 내용, 자문 내용이 바뀔 때만 갱신)
- 감염 의심 단말 네트워크 격리
- 브라우저 종료 후 서버측 세션·토큰 강제 무효화
- 비밀번호 재설정, 필요 시 OAuth 토큰·앱 비밀번호 폐기
- 최근 로그인 기록에서 비정상 IP·지역·기기 접속 점검

## 차단 운영 포맷 (복붙용)

### Domain blocklist (un-defanged, one per line — attacker infrastructure only)
```
aihealthring.com
cabinrentalsnc.com
chatbasedos.com
commercialroofingsd.com
dogtrainersgeorgia.com
fintelliganceai.com
homeinspectionsdelaware.com
intopython.com
lalandscapelighting.com
lumenagnet.com
marbellaresales.com
miamipcsupport.com
moldinspectiondayton.com
nailscanai.com
newjerseypetsitter.com
numericagent.com
oaklandwaterdamage.com
oklahomawarehousing.com
olympiapetemergency.com
peaecagent.com
plasmaticsystems.com
plethorawallet.com
premierrentalpurchase.com
ricewaterbeauty.com
rvieragent.com
sandiegotkd.com
secueragent.com
shiledagent.com
syracusefertilitycenter.com
vastbets.com
wvaeagent.com
```

### IP blocklist (un-defanged, one per line)
```
(없음)
```

## 출처
- [Hunting MacSync Stealer infrastructure through behavioral pivots](https://www.microsoft.com/en-us/security/blog/2026/08/18/hunting-macsync-stealer-infrastructure-through-behavioral-pivots/) — 2026-08-18 — fetched
- [ThreatFox IOC #1893641](https://threatfox.abuse.ch/ioc/1893641/) — 미상 — blocked
- [Beelzebub — MacSync Stealer / fake Claude Code Google Ads](https://beelzebub.ai/blog/macsync-stealer-fake-claude-code-google-ads/) — 미상 — blocked
- [CERT.az — Warning for Mac users: fake Claude AI application targets user information](https://cert.gov.az/en/news/warning-for-mac-users-fake-claude-ai-application-targets-user-information) — 미상 — blocked (DNS)
- [Trend Micro — InstallFix and Claude Code](https://www.trendmicro.com/en_us/research/26/e/installfix-and-claude-code.html) — 미상 — blocked (DNS)
- [Security Arsenal — MacSync Stealer: Hunting macOS Infostealer C2 Infrastructure With Behavioral Pivots](https://securityarsenal.com/blog/macsync-stealer-hunting-macos-infostealer-c2-infrastructure-with-behavioral-pivots-detection-and-response-guide) — 미상 — blocked (DNS)
- [Sophos — Evil evolution: ClickFix and macOS infostealers](https://www.sophos.com/en-us/blog/evil-evolution-clickfix-and-macos-infostealers) — 2026-03 — blocked (DNS)
- [SOC Prime — MacSync Stealer uses rotating C2 infrastructure](https://socprime.com/active-threats/macsync-stealer-uses-rotating-c2-infrastructure/) — 미상 — blocked (DNS)
- FSEC(금융보안원) 2026-09 보안 권고 — seed(advisory)
