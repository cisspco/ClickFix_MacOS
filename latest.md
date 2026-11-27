# MacSync Stealer (ClickFix macOS) Snapshot — 2026-11-27 UTC

## 수집 상태
- 지정 출처 접근성: Microsoft — fetched, ThreatFox — blocked, Beelzebub — blocked
- 추가 페치 성공: 없음
- 추가 페치 차단: www.huntress.com (`macsync-stealer-rat-reverse-engineering`), www.rapid7.com (`ve-clickfix-phishing-campaign-fake-claude-installer`), www.cisecurity.org (`macsync-stealer-campaign-impacting-us-sltt-macos-users`), www.bitdefender.com (`fake-claude-code-google-ads-malware`), gbhackers.com (`macsync-stealer-uses-30-rotating-domains`) — 5건 모두 DNS 조회 실패(전부 기존에도 차단 확인된 호스트, 신규 차단 도메인 없음)
- seed: 9 / 검증: 31(도메인) + 행위·URI 패턴 다수 / 미검증: 15(도메인) + 2(IP) + 5(해시)

## 캐페인 개요
가짜 Claude Desktop/Claude Code 설치 페이지(검색광고)를 통한 ClickFix 기법으로 사용자가 터미널에 명령을 직접 붙여넣게 유도하고, 아키텍처에 맞는 악성 Mach-O를 다운로드·실행. MacSync 모듈이 Chromium계·Firefox·Safari 쿠키·프로파일을 수집해 외부로 유출. 탈취된 세션 쿠키로 기존 로그인 재사용 가능.

## 공격 체인
1. 가짜 설치 페이지 유도 (검색광고) — Google/Bing 검색광고가 가짜 Claude Desktop/Claude Code 다운로드 페이지로 유도 (일부는 claude.ai 자체 콘텐츠 호스팅 기능 악용 사례도 보고됨, 별개 RAT 패밀리 혼재 — 본 저장소 범위 밖)
2. 터미널 명령 복사·붙여넣기 유도 — ClickFix 기법(T1204.004)으로 피해자가 직접 터미널에 명령 입력
3. Mach-O 다운로드 · Gatekeeper 우회 — `xattr -rd com.apple.quarantine` 후 `codesign --force --sign -` 애드혹 재서명, 아키텍처에 맞는 Mach-O 실행
4. 지속성 (cksyncd) — `~/Library/Application Support/.com.apple.airport/cksyncd` 등 정상 경로 위장 디렉터리
5. 브라우저 정보 탈취 (MacSync) — `--user-data-dir=<...cksync...>`로 Chromium 실행해 쿠키/프로파일 판독, Firefox·Safari도 대상
6. 외부 유출 · 세션 재사용 위험 — `/tmp/sync*`, `/tmp/osalogging.zip`에 스테이징 후 `/gate?buildtxd=` 등으로 청크 분할 HTTP PUT 업로드, 탈취 세션 쿠키 재사용 가능

## 공격자 인프라 — 가짜 설치/C2 (차단 대상) (31)
- aihealthring[.]com ~ wvaeagent[.]com 등 31건(Microsoft 2026-08-18, C2/피벗 인프라) — verified
- (전체 목록은 `blocklists/domains.txt` 참조. Microsoft 원문은 해당 도메인을 가짜 Claude 설치 전용 페이지로 명시하지 않으며, 행동 피벗으로 연결된 MacSync C2 인프라라고만 설명함 — 가짜 Claude 설치 페이지와의 직접 연결은 Rescana/Zscaler/Push Security/Bitdefender 등 별도의, 대부분 차단된 출처에 의존)

## 파일 해시 (5)
### SHA-256
- 173ff5ede7c28163ceaa9440de8a02cef26295f8be06b6b0f90b0a4284471bc2 — Mach-O 페이로드 "devupdatesuite-helper"(MacSync 추정, malware-traffic-analysis.net 2025-12-23, 원문 차단·검색 스니펫만) — unverified
- 1abf943e97356e07bde23663da544e7c106afc19827a2106361a52035737de43 — 트로이목마화된 Ledger Live 앱(크립토 지갑) — unverified
- 2728a7d444cd65550f652a8c66eaced0fe6d0389161f86393ab73da8f446a362 — Stage 2 zsh 로더(MacSync, 추정) — unverified
- b591bfbab57cc69ce985fbc426002ef00826605257de0547f20ebcfecc3724c2 — DMG 설치 이미지(MacSync 추정, malware-traffic-analysis.net 2025-12-23, 원문 차단·검색 스니펫만) — unverified
- bd348a40261aa2d95566ccdc4e6f304ff25aa97d34e5c713c77c937583ad04f0 — MacSync Stealer v1.1.2 드로퍼(build tag "claude1") — unverified

## 호스트 IOC · 행위 (누적)
`host-iocs.md` 참조 (경로/명령/C2 URI 패턴, 대응 권고 포함).

## 이번 실행 변경사항
- 신규 인프라: 없음
- 신규 해시: 없음
- 승격(미검증→검증): 없음
- 폐기(sinkhole/takedown): 없음
- 신규 보고서: 없음 — Microsoft(2026-08-18) 원문 재페치 결과 검증 도메인 31건 및 URI/HTTP 패턴이 기존 기록과 100% 동일(43일 연속 안정). ThreatFox·Beelzebub은 DNS 조회 실패 지속. 신규 시도 5건(www.huntress.com, www.rapid7.com, www.cisecurity.org, www.bitdefender.com, gbhackers.com) 모두 DNS 조회 실패이나 전부 기존에 이미 차단으로 확인된 호스트라 신규 차단 도메인 추가 없음. WebSearch 5건의 결과도 모두 `host-iocs.md`에 이미 기록된 내용(Huntress RAT 컴포넌트, CISecurity SLTT, Kaspersky 신버전 보도자료, Seqrite, gbhackers 30+ 회전 도메인, SOC Prime, CERT 아제르바이잔 claude1 빌드, Push Security AcSig/claude-desk-code.com 등)의 재인용만 확인 — 조용한 날(quiet day), 43일 연속 안정.

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
(없음 — 검증된 IP 없음)
```

## 출처
- [Hunting MacSync Stealer Infrastructure Through Behavioral Pivots](https://www.microsoft.com/en-us/security/blog/2026/08/18/hunting-macsync-stealer-infrastructure-through-behavioral-pivots/) — 2026-08-18 — fetched
- [ThreatFox IOC 1893641](https://threatfox.abuse.ch/ioc/1893641/) — n/a — blocked
- [Beelzebub — MacSync Stealer fake Claude Code Google Ads](https://beelzebub.ai/blog/macsync-stealer-fake-claude-code-google-ads/) — n/a — blocked
- FSEC(금융보안원) 2026-09 보안 권고 — 2026-09 — seed(advisory)
