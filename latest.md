# MacSync Stealer (ClickFix macOS) Snapshot — 2026-10-06 UTC

## 수집 상태
- 지정 출처 접근성: Microsoft(2026-08-18) — fetched, ThreatFox — blocked, Beelzebub — blocked
- 추가 페치 성공: 없음
- 추가 페치 차단: rapid7.com, pushsecurity.com, securityonline.info, scworld.com, isc.sans.edu
- seed: 호스트 IOC 다수 / 검증: 도메인 31 / 미검증: 도메인 7, 해시 1

## 캠페인 개요
가짜 Claude Desktop/Claude Code 설치 페이지(검색광고)를 통한 ClickFix 기법으로 사용자가 터미널에 명령을 직접 붙여넣게 유도하고, 아키텍처에 맞는 악성 Mach-O를 다운로드·실행. MacSync 모듈이 Chromium계·Firefox·Safari 쿠키·프로파일을 수집해 외부로 유출. 탈취된 세션 쿠키로 기존 로그인 재사용 가능.

**금일 조사 메모:** Microsoft(2026-08-18) 원문을 재페치해 검증 도메인 31건과 URI/HTTP 행동 패턴(`/curl/`, `/dynamic?txd=`, `/gate?buildtxd=`, PUT/`--data-binary`, `api-key` 헤더, `upload_id`/`chunk_index`/`total_chunks`)이 2026-09-29부터 오늘까지 8회 연속 동일함을 재확인했다. 원문은 여전히 IP·해시·ASN·지속성 메커니즘·가짜 Claude 설치 전용 도메인을 제공하지 않는다고 명시. ThreatFox·Beelzebub은 8일 연속 차단. Step 2 검색에서 MacSync 신버전의 지속성 메커니즘을 더 구체화하는 서술(`com.apple.finder.agent` LaunchAgent, `.zshrc` 수정, 전역 Git `pre-commit`/`post-checkout` 훅, `$HOME/Library/Application Support/System` 백업 경로)과 기존 Stage 2 zsh 로더 해시(MD5/SHA-1/SHA-256, 2026-10-02 등재분과 동일)를 재확인했으나, 인용 원문(bleepingcomputer.com, securelist.com, gbhackers.com)은 모두 차단되어 미검증 상태를 유지한다. InstallFix/Claude Code 연관성의 신규 1차 출처로 추정되는 Rapid7·Push Security 기사를 특정·페치 시도했고, 그 외 SecurityOnline.info·SC World·SANS ISC("Macfinger" 별개 명칭 캠페인) 기사도 시도했으나 5건 모두 프록시 차단 — 신규 차단 도메인 5건으로 기록. 신규 확정 도메인/해시/IP는 없음.

## 공격 체인
1. 가짜 설치 페이지 유도 (검색광고) — "Claude Code", "Claude Code install", "Claude Code CLI" 등 검색 시 노출되는 스폰서 링크로 유도 (⚠️ 미검증, 타 벤더 스니펫 기반)
2. 터미널 명령 복사·붙여넣기 유도 — ClickFix 기법으로 피해자가 직접 명령 실행 (T1204.004)
3. Mach-O 다운로드 · Gatekeeper 우회 — `xattr -rd com.apple.quarantine` 후 `codesign --force --sign -`로 애드혹 재서명
4. 지속성 (cksyncd) — `~/Library/Application Support/.com.apple.airport/cksyncd` 등 위장 경로에 배치
5. 브라우저 정보 탈취 (MacSync) — `--user-data-dir=<...cksync...>` 프로파일로 Chromium 실행, Firefox/Safari 데이터도 수집
6. 외부 유출 · 세션 재사용 위험 — HTTP PUT + `--data-binary` (curl `-k -s --max-time 30`), 청크 업로드(`upload_id`/`chunk_index`/`total_chunks`)로 C2에 전송

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
- oklahomawarehousing[.]com — C2/행동 피벗 식별 인프라 — verified (Microsoft, 2026-08-18)
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

## 미검증 후보 인프라 (차단 금지) (7)
- focusgroovy[.]com — C2 후보, "추가 페이로드를 가져오는 C2 서버"로 언급 (Jamf로 추정, 원문 미확인) — ⚠️ (미검증)
- houstongaragedoorinstallers[.]com — C2 후보 (RST Cloud 계열, 검색 스니펫, 원문 차단) — ⚠️ (미검증)
- jacksonvillemma[.]com — C2 후보, URL `/curl/<sha256>` 패턴이 Microsoft 검증 URI 패턴과 일치 (MS-ISAC/RST Cloud 계열 추정, 검색 합성 요약, 원문 미특정) — ⚠️ (미검증)
- toria[.]app — MacSync 신버전(가짜 크립토 지갑 "Toria") 배포 사이트 후보 — 가짜 Claude 설치 페이지와 무관 가능성, 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- warpcast[.]asia — Toria.dmg 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- streamyard.appstore.com[.]mx — 가짜 StreamYard 설치 스크립트 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- slack.apple03cloudstore[.]com — 가짜 Slack 설치 스크립트 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)

## 파일 해시 (1, 전부 미검증)
### SHA-256
- 2728a7d444cd65550f652a8c66eaced0fe6d0389161f86393ab73da8f446a362 — Stage 2 zsh 로더(MacSync 추정) — ⚠️ unverified (검색 합성 요약, 원문 미특정)

참고(본 저장소 스키마 밖, SHA-256 외 해시): MD5 `277acd8e241c1341852ebcd401203ecc`, SHA-1 `7d28507870e94b809f25883dc9dae838549ddfac` — 동일 로더로 추정, `host-iocs.md`에만 기록.

## 호스트 IOC · 행위 (누적)
상세는 [`host-iocs.md`](../host-iocs.md) 참조 (경로/명령/네트워크 패턴/대응 권고 전체 누적 문서). 금일 신규: Microsoft 데이터셋 8일 연속 안정성 재확인, 지속성 메커니즘 세부화(미검증), 신규 차단 도메인 5건(rapid7.com, pushsecurity.com, securityonline.info, scworld.com, isc.sans.edu) 추가 기록.

## 이번 실행 변경사항
- 신규 인프라: 없음
- 신규 해시: 없음
- 승격(미검증→검증): 없음
- 폐기(sinkhole/takedown): 없음
- 신규 보고서: Rapid7("ClickFix-style Phishing Campaign Uses Fake Claude Installer", 차단), Push Security("InstallFix: Weaponizing malvertised install guides", 차단), SecurityOnline.info("AI Hype Hijacked: How a Fake Claude Installer Blinds Windows Security", 차단), SC World("ClickFix campaigns target macOS users via MacSync infostealer", 차단), SANS ISC("A Closer Look at Malware From the Macfinger ClickFix Campaign", 차단, 별개 캠페인명 혼동 주의)

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
- [Rapid7 — ClickFix-style Phishing Campaign Uses Fake Claude Installer](https://www.rapid7.com/blog/post/ve-clickfix-phishing-campaign-fake-claude-installer/) — 미상 — blocked
- [Push Security — InstallFix: Weaponizing malvertised install guides](https://pushsecurity.com/blog/installfix) — 미상 — blocked
- [SecurityOnline.info — AI Hype Hijacked: How a Fake Claude Installer Blinds Windows Security](https://securityonline.info/fake-claude-ai-installer-clickfix-malware-analysis/) — 미상 — blocked
- [SC World — ClickFix campaigns target macOS users via MacSync infostealer](https://www.scworld.com/news/clickfix-campaigns-target-macos-users-via-macsync-infostealer) — 미상 — blocked
- [SANS ISC — A Closer Look at Malware From the Macfinger ClickFix Campaign](https://isc.sans.edu/diary/A+Closer+Look+at+Malware+From+the+Macfinger+ClickFix+Campaign) — 미상 — blocked
- FSEC(금융보안원) 2026-09 보안 권고 — seed(advisory)
