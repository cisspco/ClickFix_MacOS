# MacSync Stealer (ClickFix macOS) Snapshot — 2026-10-07 UTC

## 수집 상태
- 지정 출처 접근성: Microsoft(2026-08-18) — fetched, ThreatFox — blocked, Beelzebub — blocked
- 추가 페치 성공: 없음
- 추가 페치 차단: cert.gov.az, securityarsenal.com, www.trendmicro.com, socprime.com, cti.ngrok.app
- seed: 호스트 IOC 다수 / 검증: 도메인 31 / 미검증: 도메인 7, 해시 3

## 캠페인 개요
가짜 Claude Desktop/Claude Code 설치 페이지(검색광고)를 통한 ClickFix 기법으로 사용자가 터미널에 명령을 직접 붙여넣게 유도하고, 아키텍처에 맞는 악성 Mach-O를 다운로드·실행. MacSync 모듈이 Chromium계·Firefox·Safari 쿠키·프로파일을 수집해 외부로 유출. 탈취된 세션 쿠키로 기존 로그인 재사용 가능.

**금일 조사 메모:** Microsoft(2026-08-18) 원문을 재페치해 검증 도메인 31건과 URI/HTTP 행동 패턴이 2026-09-29부터 오늘까지 9회 연속 동일함을 재확인했다. 원문은 여전히 IP·해시·ASN·지속성 메커니즘·가짜 Claude 설치 전용 도메인을 제공하지 않는다고 명시. ThreatFox·Beelzebub은 9일 연속 차단. Step 2 검색에서 중요한 신규 세부사항을 확인했다: MacSync Stealer가 "v1.1.2" 버전과 빌드 태그 **"claude1"**을 사용한다는 서술(SecurityArsenal/SOC Prime 계열 추정, 원문 미확인)로, 악성코드 자체가 "Claude"를 빌드 식별자로 명명하는 것은 가짜 Claude 사칭 의도를 코드 레벨에서 뒷받침하는 유의미한 세부사항이다. 동시에 신규 해시 2건(드로퍼 1건, 트로이목마화 Ledger Live 앱 1건), 신규 URL 경로 `/ledger/live`, 신규 가짜 설치 페이지 후보(Google Sites 공유 URL)를 확인했으나 모두 WebSearch 합성 요약에만 의존해 미검증 상태로 기록했다. 신규로 시도한 cert.gov.az("Warning for Mac users: fake Claude AI application" — 제목상 유망한 1차 출처), SecurityArsenal, Trend Micro 원문 URL, SOC Prime, OTX 계열 pulse(cti.ngrok.app) 5건 모두 프록시 차단되어 직접 확인은 하지 못했다. Sophos가 보도한 가짜 **ChatGPT** 설치 광고(별개 사칭 대상, 동일 MacSync 페이로드)도 확인되어 혼동 방지 목적으로 기록했다. 신규 확정 도메인/IP는 없음.

## 공격 체인
1. 가짜 설치 페이지 유도 (검색광고) — "Claude Code", "Claude Code install" 등 검색 시 노출되는 스폰서 링크, 또는 Google Sites·Squarespace 등 공유 플랫폼에 호스팅된 복제 페이지로 유도 (⚠️ 미검증, 타 벤더 스니펫 기반)
2. 터미널 명령 복사·붙여넣기 유도 — ClickFix 기법으로 피해자가 직접 명령 실행 (T1204.004)
3. Mach-O 다운로드 · Gatekeeper 우회 — `xattr -rd com.apple.quarantine` 후 `codesign --force --sign -`로 애드혹 재서명
4. 지속성 (cksyncd) — `~/Library/Application Support/.com.apple.airport/cksyncd` 등 위장 경로에 배치
5. 브라우저 정보 탈취 (MacSync) — `--user-data-dir=<...cksync...>` 프로파일로 Chromium 실행, Firefox/Safari 데이터도 수집; 신버전(v1.1.2, build "claude1")은 크립토 지갑 앱(Ledger Live 등) 트로이목마화도 포함 (⚠️ 미검증)
6. 외부 유출 · 세션 재사용 위험 — HTTP PUT + `--data-binary` (curl `-k -s --max-time 30`), 청크 업로드(`upload_id`/`chunk_index`/`total_chunks`)로 C2에 전송; 경로 `/curl`, `/dynamic`, `/gate` (verified), `/ledger/live` (⚠️ 미검증 신규)

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
- oklahomawarehousing[.]com — C2/행동 피벗 식별 인프라 (검색 스니펫에서 "claude1" 빌드와 연계된다는 신규 미검증 서술도 확인됨) — verified (Microsoft, 2026-08-18)
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

(참고: `sites.google.com/view/claud-version-0505`는 공유 플랫폼(Google Sites) 상의 특정 경로이며 도메인 자체는 정상 서비스이므로 어떤 블록리스트에도 등재하지 않음 — host-iocs.md 참조)

## 파일 해시 (3, 전부 미검증)
### SHA-256
- 1abf943e97356e07bde23663da544e7c106afc19827a2106361a52035737de43 — 트로이목마화된 Ledger Live 앱(크립토 지갑) — ⚠️ unverified (검색 합성 요약, 원문 미특정) (2026-10-07 신규)
- 2728a7d444cd65550f652a8c66eaced0fe6d0389161f86393ab73da8f446a362 — Stage 2 zsh 로더(MacSync 추정) — ⚠️ unverified (검색 합성 요약, 원문 미특정)
- bd348a40261aa2d95566ccdc4e6f304ff25aa97d34e5c713c77c937583ad04f0 — MacSync Stealer v1.1.2 드로퍼 (build tag "claude1") — ⚠️ unverified (검색 합성 요약, 원문 미특정) (2026-10-07 신규)

참고(본 저장소 스키마 밖, SHA-256 외 해시): MD5 `277acd8e241c1341852ebcd401203ecc`, SHA-1 `7d28507870e94b809f25883dc9dae838549ddfac` — Stage 2 zsh 로더와 동일 추정, `host-iocs.md`에만 기록.

## 호스트 IOC · 행위 (누적)
상세는 [`host-iocs.md`](../host-iocs.md) 참조 (경로/명령/네트워크 패턴/대응 권고 전체 누적 문서). 금일 신규: Microsoft 데이터셋 9일 연속 안정성 재확인, MacSync "v1.1.2"/빌드 태그 "claude1" 신규 확인(미검증), 신규 URL 경로 `/ledger/live`(미검증), 신규 가짜 설치 페이지 후보(Google Sites, 미검증), 가짜 ChatGPT 광고–MacSync 연계(Sophos, 미검증·맥락), 신규 차단 도메인 5건(cert.gov.az, securityarsenal.com, www.trendmicro.com, socprime.com, cti.ngrok.app) 추가 기록.

## 이번 실행 변경사항
- 신규 인프라: 없음
- 신규 해시: 2건 (unverified) — `bd348a40...` (MacSync v1.1.2 드로퍼, build tag "claude1"), `1abf943e...` (트로이목마화 Ledger Live)
- 승격(미검증→검증): 없음
- 폐기(sinkhole/takedown): 없음
- 신규 보고서: cert.gov.az("Warning for Mac users: fake Claude AI application targets user information" — 차단), SecurityArsenal("MacSync Stealer – Hunting macOS Infostealer C2 Infrastructure with Behavioral Pivots" — 차단), Trend Micro 원문 URL("InstallFix and Claude Code" — 차단), SOC Prime("MacSync Stealer uses rotating C2 infrastructure" — 차단), cti.ngrok.app OTX pulse(차단)

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
- [CERT.az — Warning for Mac users: fake Claude AI application targets user information](https://cert.gov.az/en/news/warning-for-mac-users-fake-claude-ai-application-targets-user-information) — 미상 — blocked
- [SecurityArsenal — MacSync Stealer: Hunting macOS Infostealer C2 Infrastructure with Behavioral Pivots](https://securityarsenal.com/blog/macsync-stealer-hunting-macos-infostealer-c2-infrastructure-with-behavioral-pivots-detection-and-response-guide) — 미상 — blocked
- [Trend Micro — InstallFix and Claude Code: How Fake Install Pages Lead to Real Compromise](https://www.trendmicro.com/en_us/research/26/e/installfix-and-claude-code.html) — 미상 — blocked
- [SOC Prime — MacSync Stealer Uses Rotating C2 Infrastructure](https://socprime.com/active-threats/macsync-stealer-uses-rotating-c2-infrastructure/) — 미상 — blocked
- [OTX Pulse (cti.ngrok.app mirror)](https://cti.ngrok.app/pulses/69d7ed2e323d7edb856fa161) — 미상 — blocked
- [Sophos — Evil evolution: ClickFix and macOS infostealers](https://www.sophos.com/en-us/blog/evil-evolution-clickfix-and-macos-infostealers) — 미상 — 미시도(검색 스니펫만, ChatGPT 사칭 별개 맥락)
- FSEC(금융보안원) 2026-09 보안 권고 — seed(advisory)
