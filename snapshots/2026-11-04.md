# MacSync Stealer (ClickFix macOS) Snapshot — 2026-11-04 UTC

## 수집 상태
- 지정 출처 접근성: Microsoft — fetched, ThreatFox — blocked, Beelzebub — blocked
- 추가 페치 성공: 없음
- 추가 페치 차단: cert.gov.az, hackread.com, securityarsenal.com, moonlock.com, www.pulse.bot — 전부 DNS 조회 실패(프록시 차단). securityarsenal.com은 기존에도 차단 확인된 도메인, 나머지 4건은 이번 실행에서 신규로 차단 확인됨
- seed: 7건 (호스트 경로/명령 패턴) / 검증: 도메인 31 + C2 행위 패턴 전반 / 미검증: 도메인 15 · IP 2 · 해시 3

## 캠페인 개요
가짜 Claude Desktop/Claude Code 설치 페이지(검색광고)를 통한 ClickFix 기법으로 사용자가 터미널에 명령을 직접 붙여넣게 유도하고, 아키텍처에 맞는 악성 Mach-O를 다운로드·실행. MacSync 모듈이 Chromium계·Firefox·Safari 쿠키·프로파일을 수집해 외부로 유출. 탈취된 세션 쿠키로 기존 로그인 재사용 가능 — 비밀번호 변경·MFA 활성화만으로는 무효화되지 않음.

## 공격 체인
1. 가짜 설치 페이지 유도 (검색광고) — "claude mac" 등 검색 시 노출되는 Google 스폰서 광고가 가짜 Claude Desktop/Claude Code 다운로드 페이지로 연결. "Adception" 변종(⚠️ 미검증)은 Bing 검색결과 리디렉션과 피해자 검증(클로킹)을 거쳐 감염된 제3자 WordPress 사이트를 경유.
2. 터미널 명령 복사·붙여넣기 유도 — 가짜 페이지가 Anthropic 정식 설치 명령을 화면에 표시하지만 "복사" 버튼이 실제로는 다른 악성 명령을 클립보드에 주입(⚠️ 미검증, Adception 서술).
3. Mach-O 다운로드 · Gatekeeper 우회 — `xattr -rd com.apple.quarantine`으로 격리 속성 제거 후 `codesign --force --sign -`로 애드혹 재서명, `open -g -j -n -a --args`로 숨김 실행(seed).
4. 지속성 (cksyncd) — `~/Library/Application Support/.com.apple.airport/cksyncd` · `.cksyncd.v`에 은닉(seed). 신버전은 LaunchAgent/`.zshrc`/Git hooks 지속성을 사용한다는 서술도 있음(⚠️ 미검증).
5. 브라우저 정보 탈취 (MacSync) — `--user-data-dir=<...cksync...>`로 Chromium 실행해 쿠키 등 브라우저 데이터 판독(seed). Firefox·Safari도 대상.
6. 외부 유출 · 세션 재사용 위험 — `/curl/`, `/dynamic?txd=`, `/gate?buildtxd=` URI 패턴, HTTP PUT + `--data-binary`, `api-key` 헤더, `upload_id`/`chunk_index`/`total_chunks` 청크 업로드로 외부 서버에 유출(verified). 스테이징/C2 IP 193.29.224.151(Aeza Group 방탄호스팅 추정)·유인 도메인 profitnow[.]io로의 통신 서술도 있음(⚠️ 미검증). 탈취된 세션 쿠키로 기존 로그인 재사용 가능.

## 공격자 인프라 — 가짜 설치/C2 (차단 대상) (31)
- aihealthring[.]com, cabinrentalsnc[.]com, chatbasedos[.]com, commercialroofingsd[.]com, dogtrainersgeorgia[.]com, fintelliganceai[.]com, homeinspectionsdelaware[.]com, intopython[.]com, lalandscapelighting[.]com, lumenagnet[.]com, marbellaresales[.]com, miamipcsupport[.]com, moldinspectiondayton[.]com, nailscanai[.]com, newjerseypetsitter[.]com, numericagent[.]com, oaklandwaterdamage[.]com, oklahomawarehousing[.]com, olympiapetemergency[.]com, peaecagent[.]com, plasmaticsystems[.]com, plethorawallet[.]com, premierrentalpurchase[.]com, ricewaterbeauty[.]com, rvieragent[.]com, sandiegotkd[.]com, secueragent[.]com, shiledagent[.]com, syracusefertilitycenter[.]com, vastbets[.]com, wvaeagent[.]com — 가짜 설치/C2 도메인 — verified (Microsoft, 2026-08-18; 오늘 재페치로 재확인, 변경 없음)

## 미검증 후보 — 참고용, 차단 금지 (도메인 15 / IP 2)
- agenticsora[.]com, arkypc[.]com, claude-desk-code[.]com, cladesktop-apps[.]com, cladesktop.gitlab[.]io, focusgroovy[.]com, houstongaragedoorinstallers[.]com, jacksonvillemma[.]com, lake-90[.]com, malwareaudit[.]com, profitnow[.]io, slack.apple03cloudstore[.]com, streamyard.appstore.com[.]mx, toria[.]app, warpcast[.]asia — 검색 스니펫/원문 차단 기반 후보 — ⚠️ (미검증)
- 45.94.47[.]204 — malware-traffic-analysis.net 추정 서술 기반 후보 C2 IP — ⚠️ (미검증)
- 193.29.224[.]151 — Aeza Group(방탄호스팅) 소속 추정 스테이징/C2 IP, profitnow[.]io와 연관 — 검색 합성 요약 기반, 원문 미확인 — ⚠️ (미검증)

## 파일 해시 (3, 전부 미검증)
### SHA-256
- bd348a40261aa2d95566ccdc4e6f304ff25aa97d34e5c713c77c937583ad04f0 — MacSync Stealer v1.1.2 드로퍼 (build tag "claude1") — ⚠️ (미검증)
- 2728a7d444cd65550f652a8c66eaced0fe6d0389161f86393ab73da8f446a362 — Stage 2 zsh loader (MacSync, 추정) — ⚠️ (미검증)
- 1abf943e97356e07bde23663da544e7c106afc19827a2106361a52035737de43 — 트로이목마화된 Ledger Live 앱 (크립토 지갑) — ⚠️ (미검증)

## 호스트 IOC · 행위 (누적 — 요약, 전체는 host-iocs.md 참고)
- `~/Library/Application Support/.com.apple.airport/`, `cksyncd`, `.cksyncd.v` — 지속성 은닉 경로 — seed
- `xattr -rd com.apple.quarantine`, `codesign --force --sign -`, `open -g -j -n -a --args` — Gatekeeper 우회·숨김 실행 — seed
- `--user-data-dir=<...cksync...>` — Chromium 브라우저 데이터 판독 — seed
- `/tmp/sync*`, `/tmp/osalogging.zip` — 스테이징/임시 파일 — verified (Microsoft)
- `/curl/`, `/dynamic?txd=`, `/gate?buildtxd=` URI 패턴, PUT+`--data-binary`, `api-key` 헤더, `upload_id`/`chunk_index`/`total_chunks` — C2/업로드 패턴 — verified (Microsoft)

## 이번 실행 변경사항
- 신규 인프라: 없음
- 신규 해시: 없음
- 승격(미검증→검증): 없음
- 폐기(sinkhole/takedown): 없음
- 신규 보고서: IOC 없이 제목/맥락만 신규 확인 (원문 전부 차단) — AhnLab("Fake Claude Download Sites Spreading Malware"), hackread.com("ClickFix Attack Targets Devs with MacSync Malware via Fake Claude Tools" — 제목상 본 캠페인과 직접 연관된 1차 출처로 추정), CERT 아제르바이잔("Warning for Mac users: fake Claude AI application targets user information" — MacSync Stealer v1.1.2·빌드 태그 "claude1"을 가짜 Claude AI 앱 배포와 명시적으로 연결, 2026-10-07 기록 서술을 추가 매체로 보강)을 검색 스니펫으로 신규 확인했으나 3건 모두 원문 차단으로 구체 IOC 미확보. **주의:** 동일 검색에서 Gen Digital이 "claude.ai 자체에 가짜 설치 안내를 호스팅하는 캠페인 2건"을 보도했다는 서술이 발견됨 — 원문 미확인이며, 설령 사실이더라도 이는 피싱 콘텐츠가 정식 플랫폼에 업로드/삽입된 것(제3자 콘텐츠 호스팅 악용)을 의미할 뿐 `claude.ai` 도메인 자체가 공격자 인프라가 되는 것은 아님 — 본 저장소 원칙(`anthropic.com`/`claude.ai` 블록리스트 절대 미등재)을 재확인하는 맥락 정보로만 기록, IOC 미채택. Microsoft 1차 출처는 재페치 결과 기존 기록과 100% 동일(신규 없음). 조용한 날(quiet day) — 기존 seed/verified/unverified 데이터로 리포트 작성.

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
- [CERT Azerbaijan: Warning for Mac users — fake Claude AI application](https://cert.gov.az/en/news/warning-for-mac-users-fake-claude-ai-application-targets-user-information) — 날짜 미확인 — blocked
- [hackread.com: ClickFix Attack Targets Devs with MacSync Malware via Fake Claude Tools](https://hackread.com/clickfix-attack-devs-macsync-malware-fake-claude-tools/) — 날짜 미확인 — blocked
- [SecurityArsenal: MacSync Stealer — Hunting macOS Infostealer C2 Infrastructure](https://securityarsenal.com/blog/macsync-stealer-hunting-macos-infostealer-c2-infrastructure-with-behavioral-pivots-detection-and-response-guide) — 날짜 미확인 — blocked
- [Moonlock: ClickFix scams evolve to infect Macs with infostealers](https://moonlock.com/clickfix-scams-infect-macs-infostealers) — 날짜 미확인 — blocked
- [pulse.bot: Google Ads and Claude AI abused to spread MacSync malware via ClickFix](https://www.pulse.bot/cybersecurity/news/google-ads-and-claude-ai-abused-to-spread-macsync-malware-via-clickfix-43854487-693b-433c-8ddc-43dd6dc9446e/) — 날짜 미확인 — blocked
- FSEC(금융보안원) 2026-09 보안 권고 — 2026-09 — seed(advisory)
