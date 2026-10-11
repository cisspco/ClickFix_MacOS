# MacSync Stealer (ClickFix macOS) Snapshot — 2026-10-11 UTC

## 수집 상태
- 지정 출처 접근성: Microsoft(2026-08-18) — fetched, ThreatFox — blocked, Beelzebub — blocked
- 추가 페치 성공: 없음
- 추가 페치 차단: hackmag.com, securityarsenal.com, cti.ngrok.app, cert.gov.az, moonlock.com
- seed: 호스트 IOC 다수 / 검증: 도메인 31 / 미검증: 도메인 9, 해시 3

## 캠페인 개요
가짜 Claude Desktop/Claude Code 설치 페이지(검색광고)를 통한 ClickFix 기법으로 사용자가 터미널에 명령을 직접 붙여넣게 유도하고, 아키텍처에 맞는 악성 Mach-O를 다운로드·실행. MacSync 모듈이 Chromium계·Firefox·Safari 쿠키·프로파일을 수집해 외부로 유출. 탈취된 세션 쿠키로 기존 로그인 재사용 가능.

**금일 조사 메모:** Microsoft(2026-08-18) 원문을 재페치해 검증 도메인 31건과 URI/HTTP 행동 패턴(`/curl/`, `/dynamic?txd=`, `/gate?buildtxd=`, PUT `--data-binary`, `api-key` 헤더, User-Agent, `upload_id`/`chunk_index`/`total_chunks`)이 2026-09-29부터 오늘까지 **13회 연속** 100% 동일함을 재확인했다. 원문은 여전히 IP·해시·ASN·지속성 메커니즘·가짜 Claude 설치 전용 도메인을 제공하지 않는다고 명시. ThreatFox·Beelzebub은 13일 연속 차단(DNS 조회 실패). Step 2 검색에서 신규로 다음을 확인: (1) hackmag.com이 "MacSync Dropper Bypasses Gatekeeper Protection in macOS" 제목으로 보도했다는 서술(원문 DNS 차단, 기존 Gatekeeper 우회 seed 서술과 일치, 신규 세부사항 없음); (2) Sophos X-Ops가 2025-11부터 MacSync를 유포한 3건의 ClickFix 캠페인을 추적했다는 서술이 더 구체화됨 — 1차(2025-11)는 ChatGPT Atlas 검색광고 → 비밀번호 요구 → Mach-O 다운로드, 2차(2025-12)는 공유된 ChatGPT 대화 링크(정식 사이트) → 가짜 GitHub풍 설치 페이지(원문 sophos.com 금일도 미시도·과거 차단 이력, 기존 2026-10-07/09 "가짜 ChatGPT 설치 광고 연계" 미검증 서술을 구체화); (3) Huntress가 2026-08에 Go 언어 기반 macOS 자격증명 스틸러를 분석했다는 서술(EverWan 재인용, 원문 미확인) — 기존에 기록된 MacSync 신버전(Objective-C/Swift 전환, 2026-09-25)과 구현 언어가 달라 **동일 멀웨어인지 별개 패밀리인지 불명확**, 혼동 방지 목적으로만 기록, IOC 미채택; (4) Vega(threats.vega.io) 브리핑이 별도 Microsoft 출처를 인용해 "250개 이상의 프런트엔드 도메인"을 확인했다는 서술 — 본 저장소가 추적하는 1차 Microsoft(2026-08-18, 도메인 31건) 리포트와는 다른 Microsoft 문서로 추정되며, 서로 다른 모니터링 기간/범위일 가능성이 있어 원문 미확보 상태로는 도메인 수를 31건에서 변경하지 않음 — ⚠️ (미검증, 출처 특정 필요). CERT 아제르바이잔·securityarsenal·cti.ngrok.app(OTX 펄스)·moonlock.com 신규/재시도 페치는 전부 DNS 차단. 신규 확정 도메인/IP/해시는 없음.

## 공격 체인
1. 가짜 설치 페이지 유도 (검색광고) — "Claude Code", "Claude Code install" 등 검색 시 노출되는 스폰서 링크, 또는 공유 플랫폼(Google Sites·Squarespace 등)에 호스팅된 복제 페이지로 유도 (⚠️ 미검증, 타 벤더 스니펫 기반)
2. 터미널 명령 복사·붙여넣기 유도 — ClickFix 기법으로 피해자가 직접 명령 실행 (T1204.004)
3. Mach-O 다운로드 · Gatekeeper 우회 — `xattr -rd com.apple.quarantine` 후 `codesign --force --sign -`로 애드혹 재서명
4. 지속성 (cksyncd) — `~/Library/Application Support/.com.apple.airport/cksyncd` 등 위장 경로에 배치
5. 브라우저 정보 탈취 (MacSync) — `--user-data-dir=<...cksync...>` 프로파일로 Chromium 실행, Firefox/Safari 데이터도 수집; 신버전(v1.1.2, build "claude1"/"symbiot")은 가짜 비밀번호 프롬프트(PAM API 검증, ⚠️ 미검증)와 크립토 지갑 앱(Ledger Live 등) 트로이목마화도 포함 (⚠️ 미검증)
6. 외부 유출 · 세션 재사용 위험 — HTTP PUT + `--data-binary` (curl `-kfsSL`, `-k -s --max-time 30`, `-w %{http_code}`), 청크 업로드(`upload_id`/`chunk_index`/`total_chunks`)로 C2에 전송; 경로 `/curl`, `/dynamic`, `/gate` (verified), `/ledger/live` (⚠️ 미검증); 스테이징 `/tmp/sync*`·`/tmp/osalogging.zip`는 유출 후 삭제 (verified)

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

## 미검증 후보 인프라 (차단 금지) (9)
- agenticsora[.]com — Stage 2 로더 후보 도메인 (Huntress 계열로 추정, 검색 합성 요약, 원문 미확인) — ⚠️ (미검증)
- focusgroovy[.]com — C2 후보, "추가 페이로드를 가져오는 C2 서버"로 언급 (Jamf로 추정, 원문 미확인) — ⚠️ (미검증)
- houstongaragedoorinstallers[.]com — C2 후보 (RST Cloud 계열, 검색 스니펫, 원문 차단) — ⚠️ (미검증)
- jacksonvillemma[.]com — C2 후보, URL `/curl/<sha256>` 패턴이 Microsoft 검증 URI 패턴과 일치 (MS-ISAC/RST Cloud 계열 추정, 검색 합성 요약, 원문 미특정) — ⚠️ (미검증)
- malwareaudit[.]com — Stage 2 로더 후보 도메인 (Huntress 계열로 추정, 검색 합성 요약, 원문 미확인) — ⚠️ (미검증)
- slack.apple03cloudstore[.]com — 가짜 Slack 설치 스크립트 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- streamyard.appstore.com[.]mx — 가짜 StreamYard 설치 스크립트 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- toria[.]app — MacSync 신버전(가짜 크립토 지갑 "Toria") 배포 사이트 후보 — 가짜 Claude 설치 페이지와 무관 가능성, 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)
- warpcast[.]asia — Toria.dmg 호스팅 후보 — 검색 합성 요약만 — ⚠️ (미검증, 범위 불확실)

(참고: `sites.google.com/view/claud-version-0505`, 정식 `claude.ai` 공개 페이지상 게시된 악성 명령 사례는 정상 서비스/도메인 위 콘텐츠 악용이므로 도메인 자체를 어떤 블록리스트에도 등재하지 않음 — host-iocs.md 참조)

## 파일 해시 (3, 전부 미검증)
### SHA-256
- 1abf943e97356e07bde23663da544e7c106afc19827a2106361a52035737de43 — 트로이목마화된 Ledger Live 앱(크립토 지갑) — ⚠️ unverified (검색 합성 요약, 원문 미특정)
- 2728a7d444cd65550f652a8c66eaced0fe6d0389161f86393ab73da8f446a362 — Stage 2 zsh 로더(MacSync 추정) — ⚠️ unverified (검색 합성 요약, 원문 미특정)
- bd348a40261aa2d95566ccdc4e6f304ff25aa97d34e5c713c77c937583ad04f0 — MacSync Stealer v1.1.2 드로퍼 (build tag "claude1") — ⚠️ unverified (검색 합성 요약, 원문 미특정)

참고(본 저장소 스키마 밖, SHA-256 외 해시): MD5 `277acd8e241c1341852ebcd401203ecc`, SHA-1 `7d28507870e94b809f25883dc9dae838549ddfac` — Stage 2 zsh 로더와 동일 추정, `host-iocs.md`에만 기록.

## 호스트 IOC · 행위 (누적)
상세는 [`host-iocs.md`](../host-iocs.md) 참조 (경로/명령/네트워크 패턴/대응 권고 전체 누적 문서). 금일 신규: Microsoft 데이터셋 13일 연속 안정성 재확인, hackmag.com(신규 차단 도메인) 및 재시도 4건 전부 차단, Sophos 3단계 캠페인 서술 구체화(날짜·유인 세분화), Huntress Go 기반 스틸러 혼동 주의 항목 추가, Vega발 "별도 Microsoft 출처 250+ 도메인" 서술 주의 항목 추가. 실질적 신규 IOC는 없음.

## 이번 실행 변경사항
- 신규 인프라: 없음
- 신규 해시: 없음
- 승격(미검증→검증): 없음
- 폐기(sinkhole/takedown): 없음
- 신규 보고서: hackmag.com(차단, 신규), securityarsenal.com(차단, 재시도), cti.ngrok.app(차단, 신규), cert.gov.az(차단, 재시도), moonlock.com(차단, 신규) — 전부 DNS 조회 실패로 원문 미확보

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
- [HackMag — MacSync Dropper Bypasses Gatekeeper Protection in macOS](https://hackmag.com/news/macsync) — 미상 — blocked
- [Security Arsenal — MacSync Stealer ClickFix fake CAPTCHA campaign, crypto-draining Mach-O payloads](https://securityarsenal.com/blog/macsync-stealer-clickfix-campaign-fake-captcha-terminal-commands-deploy-crypto-draining-mach-o-payloads-otx-pulse-analysis-and-detection-pack) — 미상 — blocked
- [AlienVault OTX pulse (cti.ngrok.app mirror)](https://cti.ngrok.app/pulses/69d7ed2e323d7edb856fa161) — 미상 — blocked
- [CERT Azerbaijan — Warning for Mac users: fake Claude AI application targets user information](https://cert.gov.az/en/news/warning-for-mac-users-fake-claude-ai-application-targets-user-information) — 미상 — blocked
- [Moonlock — ClickFix scams evolve to infect Macs with infostealers](https://moonlock.com/clickfix-scams-infect-macs-infostealers) — 미상 — blocked
- FSEC(금융보안원) 2026-09 보안 권고 — seed(advisory)
