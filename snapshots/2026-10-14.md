# MacSync Stealer (ClickFix macOS) Snapshot — 2026-10-14 UTC

## 수집 상태
- 지정 출처 접근성: Microsoft(2026-08-18) — fetched, ThreatFox — blocked, Beelzebub — blocked
- 추가 페치 성공: 없음
- 추가 페치 차단: malwarebytes.com, moonlock.com, hackmag.com, bleepingcomputer.com, securityarsenal.com
- seed: 호스트 IOC 다수 / 검증: 도메인 31 / 미검증: 도메인 14, IP 1, 해시 3

## 캠페인 개요
가짜 Claude Desktop/Claude Code 설치 페이지(검색광고)를 통한 ClickFix 기법으로 사용자가 터미널에 명령을 직접 붙여넣게 유도하고, 아키텍처에 맞는 악성 Mach-O를 다운로드·실행. MacSync 모듈이 Chromium계·Firefox·Safari 쿠키·프로파일을 수집해 외부로 유출. 탈취된 세션 쿠키로 기존 로그인 재사용 가능.

**금일 조사 메모:** Microsoft(2026-08-18) 원문을 재페치해 검증 도메인 31건 및 호스트/네트워크 패턴이 16회 연속 100% 동일함을 재확인. ThreatFox·Beelzebub은 16일 연속 DNS 차단. 신규로 malwarebytes.com("가짜 Claude 검색결과" 보도)과 hackmag.com("Claude 아티팩트 악용" 보도) 원문 페치를 시도했으나 둘 다 DNS 차단되어 미확보. 금일 가장 중요한 성과는 검색 스니펫을 통해 2026-04-22 Malware Traffic Analysis 트래픽 분석 게시물에서 언급된 또 다른 가짜 Claude 다운로드 변종 체인(`cladesktop.gitlab.io` → `arkypc.com` → C2 `45.94.47.204`)과, URL 스캐너(malwaretips.com)가 Anthropic Claude AI 사칭으로 플래그한 `cladesktop-apps.com`을 신규 식별한 것이다. 이는 본 저장소 사상 최초의 구체적 C2 IP 후보이나, 두 원문(malware-traffic-analysis.net, tools.malwaretips.com)은 이번 실행 페치 예산(8건) 소진으로 직접 확인하지 못해 전부 미검증 상태로만 등재했다.

## 공격 체인
1. 가짜 설치 페이지 유도 (검색광고) — "claude mac" 등 검색 시 Google 스폰서 광고 노출. "Adception" 변종(⚠️ 미검증)은 표면 도메인으로 `bing.com`을 표시해 Bing 리디렉션 후 클로킹된 침해 WordPress 사이트를 경유, 가짜 다운로드 페이지 `claude-desk-code[.]com`(⚠️ 미검증)에 도달. 별개 변종으로 추정되는 `cladesktop.gitlab[.]io` / `cladesktop-apps[.]com`(⚠️ 미검증, 금일 신규)도 가짜 Claude 다운로드 페이지로 식별됨
2. 터미널 명령 복사·붙여넣기 유도 — ClickFix 기법; 일부 변종은 화면 표시 명령과 클립보드 주입 명령이 다름(⚠️ 미검증) (T1204.004)
3. Mach-O 다운로드 · Gatekeeper 우회 — curl이 `lake-90[.]com`(⚠️ 미검증) 또는 `arkypc[.]com`(⚠️ 미검증, 금일 신규)에서 페이로드 수신 후 zsh 파이프 실행; `xattr -rd com.apple.quarantine` 후 `codesign --force --sign -`로 애드혹 재서명
4. 지속성 (cksyncd) — `~/Library/Application Support/.com.apple.airport/cksyncd` 등 위장 경로, `/tmp/sync*`·`/tmp/osalogging.zip` 스테이징(verified, Microsoft)
5. 브라우저 정보 탈취 (MacSync) — `--user-data-dir=<...cksync...>` 프로파일로 Chromium 실행, Firefox/Safari 데이터도 수집; 신버전(v1.1.2, build "claude1"/"symbiot")은 가짜 비밀번호 프롬프트와 크립토 지갑 앱(Ledger Live 등) 트로이목마화도 포함 (⚠️ 미검증)
6. 외부 유출 · 세션 재사용 위험 — HTTP PUT + `--data-binary`, 청크 업로드(`upload_id`/`chunk_index`/`total_chunks`)로 C2에 전송; 경로 `/curl`, `/dynamic`, `/gate` (verified), `/ledger/live` (⚠️ 미검증). 금일 신규 후보 C2 IP `45.94.47[.]204`(⚠️ 미검증)는 별개 변종(cladesktop)과 연관

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
- arkypc[.]com — "cladesktop" 변종 Mach-O 페이로드 호스팅 후보 (Malware Traffic Analysis, 2026-04-22, 원문 미페치) — ⚠️ (미검증) (2026-10-14 추가)
- claude-desk-code[.]com — "Adception" 변종 가짜 Claude 다운로드 페이지(화면 표시 명령과 클립보드 주입 명령이 다름) — 2차 매체 교차확인, 1차 출처 전부 차단 — ⚠️ (미검증)
- cladesktop-apps[.]com — URL 스캐너(malwaretips.com)가 Anthropic Claude AI 사칭·가짜 macOS/Windows/ChromeOS 다운로드로 플래그(탐지 데이터 오래됨), 원문 미페치 — ⚠️ (미검증, 범위/시점 불확실) (2026-10-14 추가)
- cladesktop.gitlab[.]io — "cladesktop" 변종 가짜 Claude 다운로드 사이트 (Malware Traffic Analysis, 2026-04-22, 원문 미페치) — ⚠️ (미검증) (2026-10-14 추가)
- focusgroovy[.]com — C2 후보, "추가 페이로드를 가져오는 C2 서버"로 언급 (Jamf로 추정, 원문 미확인) — ⚠️ (미검증)
- houstongaragedoorinstallers[.]com — C2 후보 (RST Cloud 계열, 검색 스니펫, 원문 차단) — ⚠️ (미검증)
- jacksonvillemma[.]com — C2 후보, URL `/curl/<sha256>` 패턴이 Microsoft 검증 URI 패턴과 일치 (MS-ISAC/RST Cloud 계열 추정, 검색 합성 요약, 원문 미특정) — ⚠️ (미검증)
- lake-90[.]com — "Adception" 변종에서 클립보드 주입 악성 명령이 `.dat` 페이로드를 받아오는 도메인(curl → zsh 파이프) — 2차 매체 교차확인, 1차 출처 전부 차단 — ⚠️ (미검증)
- malwareaudit[.]com — Stage 2 로더 후보 도메인 (Huntress 계열로 추정, 검색 합성 요약, 원문 미확인) — ⚠️ (미검증)
- slack.apple03cloudstore[.]com — 가짜 Slack 설치 스크립트 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- streamyard.appstore.com[.]mx — 가짜 StreamYard 설치 스크립트 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- toria[.]app — MacSync 신버전(가짜 크립토 지갑 "Toria") 배포 사이트 후보 — 가짜 Claude 설치 페이지와 무관 가능성, 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- warpcast[.]asia — Toria.dmg 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- **IP** 45.94.47[.]204 — "cladesktop" 변종 C2 후보 (Malware Traffic Analysis, 2026-04-22, 원문 미페치) — 본 저장소 최초의 구체적 C2 IP 후보 — ⚠️ (미검증) (2026-10-14 추가)

(참고: `sites.google.com/view/claud-version-0505`, 정식 `claude.ai` 공개 페이지상 게시된 악성 명령 사례, "Adception" 체인에서 사용된 클로킹된 침해 WordPress 사이트(정상 제3자 사이트가 탈취되어 리디렉션에 악용됨, compromised-third-party)는 정상 서비스/도메인 위 콘텐츠 악용 또는 미특정이므로 도메인 자체를 어떤 블록리스트에도 등재하지 않음 — host-iocs.md 참조)

## 파일 해시 (3, 전부 미검증)
### SHA-256
- 1abf943e97356e07bde23663da544e7c106afc19827a2106361a52035737de43 — 트로이목마화된 Ledger Live 앱(크립토 지갑) — ⚠️ unverified (검색 합성 요약, 원문 미특정)
- 2728a7d444cd65550f652a8c66eaced0fe6d0389161f86393ab73da8f446a362 — Stage 2 zsh 로더(MacSync 추정) — ⚠️ unverified (검색 합성 요약, 원문 미특정)
- bd348a40261aa2d95566ccdc4e6f304ff25aa97d34e5c713c77c937583ad04f0 — MacSync Stealer v1.1.2 드로퍼 (build tag "claude1") — ⚠️ unverified (검색 합성 요약, 원문 미특정)

참고(본 저장소 스키마 밖, SHA-256 외 해시): MD5 `277acd8e241c1341852ebcd401203ecc`, SHA-1 `7d28507870e94b809f25883dc9dae838549ddfac` — Stage 2 zsh 로더와 동일 추정, `host-iocs.md`에만 기록.

## 호스트 IOC · 행위 (누적)
상세는 [`host-iocs.md`](../host-iocs.md) 참조 (경로/명령/네트워크 패턴/대응 권고 전체 누적 문서). 금일 신규: Microsoft 데이터셋 16일 연속 안정성 재확인, "cladesktop" 변종 신규 식별(도메인 3건 + IP 1건, 전부 미검증), malwarebytes.com·hackmag.com 신규 차단 확인.

## 이번 실행 변경사항
- 신규 인프라: cladesktop.gitlab.io, arkypc.com, cladesktop-apps.com, 45.94.47.204 (전부 미검증)
- 신규 해시: 없음
- 승격(미검증→검증): 없음
- 폐기(sinkhole/takedown): 없음
- 신규 보고서: Malware Traffic Analysis "2026-04-22: malicious ad leads to ClickFix-style page for macOS malware" (원문 미페치, 검색 스니펫으로만 확인), malwaretips.com URL 스캐너 결과(cladesktop-apps.com)

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
- [BleepingComputer — hackers abuse google ads bing redirects to push claude clickfix attacks](https://www.bleepingcomputer.com/news/security/hackers-abuse-google-ads-bing-redirects-to-push-claude-clickfix-attacks/) — 2026-10-09 — blocked (2차 매체로만 확인)
- [Malwarebytes — Fake Claude search results lure Mac users into ClickFix attack](https://www.malwarebytes.com/blog/news/2026/05/fake-claude-search-results-lure-mac-users-into-clickfix-attack) — 2026-05 — blocked
- [HackMag — Hackers Are Using Claude Artifacts to Distribute macOS Malware](https://hackmag.com/news/claude-malware) — 미상 — blocked
- [Malware Traffic Analysis — 2026-04-22: malicious ad leads to ClickFix-style page for macOS malware](https://malware-traffic-analysis.net/2026/04/22/index.html) — 2026-04-22 — 미페치 (검색 스니펫만)
- [Trend Micro — InstallFix and Claude Code](https://www.trendmicro.com/en_gb/research/26/e/installfix-and-claude-code.html) — 미상 — blocked
- [Sophos X-Ops — Evil evolution: ClickFix and macOS infostealers](https://www.sophos.com/en-us/blog/evil-evolution-clickfix-and-macos-infostealers) — 2026-03(추정) — blocked
- FSEC(금융보안원) 2026-09 보안 권고 — seed(advisory)
