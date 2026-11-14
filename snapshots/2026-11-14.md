# MacSync Stealer (ClickFix macOS) Snapshot — 2026-11-14 UTC

## 수집 상태
- 지정 출처 접근성: Microsoft — fetched, ThreatFox — blocked, Beelzebub — blocked
- 추가 페치 성공: 없음
- 추가 페치 차단: www.rapid7.com, hackread.com, www.anvilogic.com, securityarsenal.com, www.scworld.com (www.scworld.com만 신규 확인, 나머지는 기존 차단 목록과 동일)
- seed: 다수(파일경로/명령 패턴) / 검증: 도메인 31 / 미검증: 도메인 15, IP 2, 해시 3

## 캐페인 개요
가짜 Claude Desktop/Claude Code 설치 페이지(검색광고)를 통한 ClickFix 기법으로 사용자가 터미널에 명령을 직접 붙여넣게 유도하고, 아키텍처에 맞는 악성 Mach-O를 다운로드·실행. MacSync 모듈이 Chromium계·Firefox·Safari 쿠키·프로파일을 수집해 외부로 유출. 탈취된 세션 쿠키로 기존 로그인 재사용 가능.

## 공격 체인
1. 가짜 설치 페이지 유도 (검색광고) — 가짜 Claude Desktop/Claude Code 다운로드 페이지가 검색 광고를 통해 노출됨
2. 터미널 명령 복사·붙여넣기 유도 — ClickFix 기법(T1204.004)으로 피해자가 터미널에 명령을 직접 붙여넣도록 유도
3. Mach-O 다운로드 · Gatekeeper 우회 — `/curl/` 경로를 통해 아키텍처에 맞는 Mach-O 바이너리 다운로드, `xattr -rd com.apple.quarantine`로 격리 속성 제거 후 `codesign --force --sign -`로 애드혹 재서명
4. 지속성 (cksyncd) — `~/Library/Application Support/.com.apple.airport/cksyncd` 등 정상 경로 위장 디렉터리에 바이너리 배치, `open -g -j -n -a --args`로 숨김 실행
5. 브라우저 정보 탈취 (MacSync) — `--user-data-dir=<...cksync...>` 옵션으로 Chromium을 실행해 쿠키·프로파일 판독, Firefox/Safari 데이터도 수집
6. 외부 유출 · 세션 재사용 위험 — HTTP PUT + `--data-binary`로 `upload_id`/`chunk_index`/`total_chunks` 청크 분할 업로드, 공유 `api-key` 헤더 사용. 탈취된 세션 쿠키로 기존 로그인 재사용 가능

## 공격자 인프라 — 가짜 설치/C2 (차단 대상) (31)
- aihealthring[.]com — C2, Microsoft — verified
- cabinrentalsnc[.]com — C2, Microsoft — verified
- chatbasedos[.]com — C2, Microsoft — verified
- commercialroofingsd[.]com — C2, Microsoft — verified
- dogtrainersgeorgia[.]com — C2, Microsoft — verified
- fintelliganceai[.]com — C2, Microsoft — verified
- homeinspectionsdelaware[.]com — C2, Microsoft — verified
- intopython[.]com — C2, Microsoft — verified
- lalandscapelighting[.]com — C2, Microsoft — verified
- lumenagnet[.]com — C2, Microsoft — verified
- marbellaresales[.]com — C2, Microsoft — verified
- miamipcsupport[.]com — C2, Microsoft — verified
- moldinspectiondayton[.]com — C2, Microsoft — verified
- nailscanai[.]com — C2, Microsoft — verified
- newjerseypetsitter[.]com — C2, Microsoft — verified
- numericagent[.]com — C2, Microsoft — verified
- oaklandwaterdamage[.]com — C2, Microsoft — verified
- oklahomawarehousing[.]com — C2, Microsoft — verified
- olympiapetemergency[.]com — C2, Microsoft — verified
- peaecagent[.]com — C2, Microsoft — verified
- plasmaticsystems[.]com — C2, Microsoft — verified
- plethorawallet[.]com — C2, Microsoft — verified
- premierrentalpurchase[.]com — C2, Microsoft — verified
- ricewaterbeauty[.]com — C2, Microsoft — verified
- rvieragent[.]com — C2, Microsoft — verified
- sandiegotkd[.]com — C2, Microsoft — verified
- secueragent[.]com — C2, Microsoft — verified
- shiledagent[.]com — C2, Microsoft — verified
- syracusefertilitycenter[.]com — C2, Microsoft — verified
- vastbets[.]com — C2, Microsoft — verified
- wvaeagent[.]com — C2, Microsoft — verified

## 미검증 후보 도메인 (차단 금지, 참고용) (15)
- agenticsora[.]com — ⚠️ (미검증)
- arkypc[.]com — ⚠️ (미검증)
- claude-desk-code[.]com — ⚠️ (미검증)
- cladesktop-apps[.]com — ⚠️ (미검증)
- cladesktop.gitlab[.]io — ⚠️ (미검증)
- focusgroovy[.]com — MacSync 신버전 추가 C2 후보, 원문 미확인 — ⚠️ (미검증)
- houstongaragedoorinstallers[.]com — ⚠️ (미검증)
- jacksonvillemma[.]com — Stage 2 zsh loader 배포 경로(`/curl/<sha256>`) 후보, 원문 미확인 — ⚠️ (미검증)
- lake-90[.]com — ⚠️ (미검증)
- malwareaudit[.]com — ⚠️ (미검증)
- profitnow[.]io — Aeza Group 호스팅 C2(193.29.224.151) 연관 유인 도메인 후보, 사칭 대상 Claude 여부 미확인 — ⚠️ (미검증)
- slack.apple03cloudstore[.]com — 가짜 Slack 설치 스크립트 경로, MacSync 신버전 연관 — ⚠️ (미검증)
- streamyard.appstore.com[.]mx — 가짜 StreamYard 설치 스크립트 경로, MacSync 신버전 연관 — ⚠️ (미검증)
- toria[.]app — 가짜 크립토 지갑 앱 "Toria" 배포 경로 — ⚠️ (미검증)
- warpcast[.]asia — "Toria.dmg" 배포 경로 후보 — ⚠️ (미검증)

## 미검증 후보 IP (차단 금지, 참고용) (2)
- 193.29.224.151 — Aeza Group(방탄호스팅) 소속, MacSync 스테이징/C2 후보 — ⚠️ (미검증)
- 45.94.47.204 — ⚠️ (미검증)

## 파일 해시 (3, 전부 미검증)
### SHA-256
- 1abf943e97356e07bde23663da544e7c106afc19827a2106361a52035737de43 — 트로이목마화된 Ledger Live 앱(크립토 지갑) — ⚠️ (미검증)
- 2728a7d444cd65550f652a8c66eaced0fe6d0389161f86393ab73da8f446a362 — Stage 2 zsh loader (MacSync, 추정) — ⚠️ (미검증)
- bd348a40261aa2d95566ccdc4e6f304ff25aa97d34e5c713c77c937583ad04f0 — MacSync Stealer v1.1.2 드로퍼 (build tag "claude1") — ⚠️ (미검증)

## 호스트 IOC · 행위 (누적)
전체 목록은 `host-iocs.md` 참조. 핵심 항목:
- `~/Library/Application Support/.com.apple.airport/`, `cksyncd`, `.cksyncd.v` — seed
- `xattr -rd com.apple.quarantine`, `codesign --force --sign -`, `open -g -j -n -a --args` — seed
- `--user-data-dir=<...cksync...>` (Chromium 브라우저 데이터 판독) — seed
- `/tmp/sync*`, `/tmp/osalogging.zip` — verified (Microsoft)
- URI 패턴 `/curl/`, `/dynamic?txd=`, `/gate?buildtxd=`; HTTP PUT + `--data-binary`; `api-key` 헤더; `upload_id`/`chunk_index`/`total_chunks`; curl 플래그 `-k -s --max-time 30 -w %{http_code} -kfsSL` — verified (Microsoft)

## 이번 실행 변경사항
- 신규 인프라: 없음
- 신규 해시: 없음
- 승격(미검증→검증): 없음
- 폐기(sinkhole/takedown): 없음
- 신규 보고서: 없음 (본 캠페인에 확정 귀속되는 신규 IOC 없음) — Microsoft 재페치 결과 기존과 100% 동일(검증 도메인 31건, URI/HTTP 패턴 동일). ThreatFox·Beelzebub은 이번에도 DNS 조회 실패. WebSearch 5건(MacSync IOC 11월, ClickFix 가짜 Claude 설치, Mach-O 구글광고, cksyncd 지속성, 세션쿠키 탈취)에서 신규로 www.rapid7.com, hackread.com, www.anvilogic.com, securityarsenal.com, www.scworld.com 5건의 직접 관련성 높은 기사(제목상 본 캠페인과 직접 연관)를 특정·페치 시도했으나 전부 DNS 조회 실패(프록시 차단) — www.scworld.com만 신규 차단 도메인으로 기록(나머지는 기존에 이미 차단 확인된 도메인). 검색 스니펫에서도 기존 기록(SOC Prime 키체인 비밀번호 GET 유출 서술, SCC Homebrew 유인 LaunchAgent 지속성 — 별개 유인·동일 패밀리, Moonlock "단계형 로더" 서술, Sophos ChatGPT Atlas 유인 3캠페인, Cato Networks 가짜 OpenAI Codex Google Sites iframe 캠페인, Huntress FakeAgent/SectopRAT, Gen Digital claude.ai 자체 호스팅)의 재인용만 확인 — 조용한 날(quiet day) — 블록리스트/해시 변경 없음.

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
(없음 — 검증된 공격자 IP 없음)
```

## 출처
- [Hunting MacSync Stealer Infrastructure Through Behavioral Pivots](https://www.microsoft.com/en-us/security/blog/2026/08/18/hunting-macsync-stealer-infrastructure-through-behavioral-pivots/) — 2026-08-18 — fetched
- [ThreatFox IOC 1893641](https://threatfox.abuse.ch/ioc/1893641/) — 날짜 미확인 — blocked
- [MacSync Stealer: Fake Claude Code Google Ads](https://beelzebub.ai/blog/macsync-stealer-fake-claude-code-google-ads/) — 날짜 미확인 — blocked
- [Rapid7: ClickFix phishing campaign — fake Claude installer](https://www.rapid7.com/blog/post/ve-clickfix-phishing-campaign-fake-claude-installer/) — 날짜 미확인 — blocked
- [Hackread: ClickFix Attack Targets Devs with MacSync Malware via Fake Claude Tools](https://hackread.com/clickfix-attack-devs-macsync-malware-fake-claude-tools/) — 날짜 미확인 — blocked
- [Anvilogic: MacSync Infostealer via ClickFix Claude Artifact Abuse](https://www.anvilogic.com/threat-reports/macsync-infostealer-via-clickfix-claude-artifact-abuse) — 날짜 미확인 — blocked
- [Security Arsenal: Hunting macOS Infostealer C2 Infrastructure with Behavioral Pivots](https://securityarsenal.com/blog/macsync-stealer-hunting-macos-infostealer-c2-infrastructure-with-behavioral-pivots-detection-and-response-guide) — 날짜 미확인 — blocked
- [SC World: Claude Code install search result leads to ClickFix infostealer attack](https://www.scworld.com/news/claude-code-install-search-result-leads-to-clickfix-infostealer-attack) — 날짜 미확인 — blocked
- FSEC(금융보안원) 2026-09 보안 권고 — 2026-09 — seed(advisory)
