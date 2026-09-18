# ClickFix_MacOS — MacSync Stealer Tracker

**MacSync Stealer**는 가짜 **Claude Desktop / Claude Code 설치 페이지**(검색광고를 통해 노출)를 이용한 ClickFix 기법으로 macOS 사용자에게 터미널 명령을 직접 붙여넣게 유도해 배포되는 macOS 인포스틸러입니다. Anthropic의 실제 제품(Claude Desktop, Claude Code)을 사칭하는 캠페인이며, 이 저장소는 방어적 탐지·차단 목적의 IOC를 일일 스냅샷으로 추적합니다.

- **최종 갱신 (UTC):** 2026-09-18
- **도메인:** 31 (검증 31 / 미검증 0)
- **IP:** 0 (검증 0 / 미검증 0)
- **해시:** 0
- **미검증 후보 (별도 파일, 차단 금지):** 도메인 1 / IP 0

## 차단용 raw 파일

- 도메인 블록리스트: https://raw.githubusercontent.com/cisspco/ClickFix_MacOS/main/blocklists/domains.txt
- IP 블록리스트: https://raw.githubusercontent.com/cisspco/ClickFix_MacOS/main/blocklists/ips.txt

**위 두 파일(`blocklists/domains.txt`, `blocklists/ips.txt`)만 방화벽/차단 시스템에 직접 투입해도 안전합니다.** 그 외 파일(`blocklists/unverified-*.txt`, `hashes/sha256.txt`, `host-iocs.md`, `snapshots/*.md`)은 참고·조사용이며 자동 차단에 사용하지 마십시오.

⚠️ **경고:** 정식 `anthropic.com` / `claude.ai` 도메인 및 광고 플랫폼(예: Google 계열 도메인)은 공격자 인프라가 아니며, 어떤 상황에서도 이 저장소의 블록리스트에 포함되어서는 안 됩니다. 포함되어 있다면 오류이니 즉시 보고 바랍니다.

## 저장소 구조

- `snapshots/<YYYY-MM-DD>.md` — 일일 스냅샷 리포트 (한국어, defanged)
- `latest.md` — 최신 스냅샷과 동일한 내용
- `blocklists/domains.txt`, `blocklists/ips.txt` — 공격자 인프라 전용, un-defanged, 차단 투입용
- `blocklists/unverified-domains.txt`, `blocklists/unverified-ips.txt` — 미검증 후보 (차단 금지)
- `hashes/sha256.txt` — 악성 Mach-O/MacSync 모듈 해시 (누적)
- `host-iocs.md` — 호스트 경로/명령/지속성 IOC 및 대응 권고 (누적)

## 출처

FSEC(금융보안원) 2026-09 보안 권고를 시드로, Microsoft Security Blog 등 공개 위협 인텔리전스 리포트를 정기적으로 취합합니다. 각 스냅샷의 "출처" 절에서 개별 리포트의 접근 가능 여부(fetched/blocked)를 기록합니다.
