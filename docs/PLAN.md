# 개발 일정 및 체크리스트

마감: **2026-12-06 (일) 23:59** · 제출물: Repository URL + 소스코드 zip (e-Class)

## Stage 0 — 준비 (11/2 ~ 11/4)
- [ ] GitHub 저장소 생성, 기본 파일 커밋
- [ ] LICENSE, README의 `<YOUR_NAME>`, `<YOUR_GITHUB_ID>` 수정
- [ ] 가상환경 생성, `pip install -r requirements.txt` 확인
- [ ] FastF1으로 세션 하나 로드되는지 확인

## Stage 1 — 최적 라인 분석 앱 (11/5 ~ 11/15) ⭐ 최소 제출선
- [ ] `track.py`: 위치 데이터 로드, 중심선 추출, 트랙 폭
- [ ] `optimizer.py`: 최소 곡률 최적 라인
- [ ] `vehicle.py`: 속도 프로파일, 예상 랩타임
- [ ] `export.py` + `scripts/build_tracks.py`: 서킷 JSON 3개 이상
- [ ] `apps/analysis_app.py`: 파라미터 조절, 지도·그래프, 폴 랩 비교
- [ ] 테스트 작성
- [ ] README에 분석 앱 스크린샷 추가

## Stage 2 — PC 주행 게임 (11/16 ~ 11/24)
- [ ] FastAPI 서버, 서킷 JSON API
- [ ] Canvas 서킷 렌더링, 카메라
- [ ] 차량 물리, 트랙 이탈 처리
- [ ] 랩 타이머, 섹터
- [ ] 고스트(최적 라인, 폴 랩)
- [ ] 키보드로 한 바퀴 완주 확인

## Stage 3 — 폰 컨트롤러 (11/25 ~ 11/30)
- [ ] 첫날: HTTPS(mkcert/cloudflared) + 폰 센서 값 읽기 확인
- [ ] WebSocket 중계, 방 코드
- [ ] QR 코드 접속
- [ ] 기울기 조향, 터치 가속/브레이크
- [ ] iPhone / Android 각각 테스트

## Stage 4 — 비교 분석 + 문서화 (12/1 ~ 12/6)
- [ ] 주행 기록 저장
- [ ] 내 라인 vs 최적 라인 vs 폴 랩 비교 화면
- [ ] README 완성 (스크린샷, 데모 GIF/영상, 설치·실행법, 동작 원리, 한계, References)
- [ ] 새 환경에서 README대로 설치·실행 재확인
- [ ] 제출: Repository URL + zip

## Bonus
- [ ] 2인 대결
- [ ] 리더보드
- [ ] 강화학습 에이전트
