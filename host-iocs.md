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

## 미검증 항목

- API 키 `5190ef1733183a0dc63fb623357f56d6` — C2 인증(`api-key` 헤더) 값으로 추정. 언급한 원문(RST Cloud/Huntress 계열)이 프록시에 의해 차단되어 검색 스니펫으로만 확인됨 — ⚠️ (미검증)
- houstongaragedoorinstallers[.]com — 위 API 키와 함께 언급된 신규 C2 후보 도메인(RST Cloud, 검색 스니펫, 원문 차단) — ⚠️ (미검증) (2026-09-11 추가)

## 대응 권고 (고정 — 자문 내용이 바뀔 때만 갱신)

- 감염 의심 단말은 즉시 네트워크에서 격리
- 브라우저 전체 종료 후 서버측 세션·토큰 강제 무효화 (비밀번호 변경/MFA 활성화만으로는 이미 탈취된 세션 쿠키가 무효화되지 않음)
- 비밀번호 재설정, 필요 시 OAuth 토큰·앱 비밀번호 폐기
- 최근 로그인 기록에서 비정상 IP·지역·기기 접속 여부 점검
- 상기 파일 경로(`.com.apple.airport`, `cksyncd`, `/tmp/sync*`, `/tmp/osalogging.zip`) 및 LaunchAgent/Daemon 존재 여부 EDR/수동 점검
- `xattr`, `codesign --force --sign -`, `open -g -j -n -a` 조합의 터미널 명령 실행 이력 로그 점검
