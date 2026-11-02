# CLAUDE.md — Apex Line 프로젝트 안내서

이 파일은 Claude Code가 이 저장소에서 작업할 때 따르는 기준이다. 작업을 시작하기 전에 항상 이 파일과 `docs/PLAN.md`를 먼저 읽는다.

## 1. 프로젝트 개요

**Apex Line**: 실제 F1 주행 데이터(FastF1)로 서킷을 재구성하고, 최적 레이싱 라인을 계산·분석하며, 그 서킷 위에서 스마트폰을 컨트롤러로 사용해 직접 차를 몰아볼 수 있는 오픈소스 프로젝트.

- 대학 "오픈소스소프트웨어" 텀프로젝트 (마감: 2026-12-06 23:59 KST)
- 평가: 아이디어/창의성, 완성도, 난이도 (상대평가)
- 라이선스: MIT

### 핵심 흐름
1. **분석**: 서킷/연도/드라이버 선택 → 중심선 추출 → 최소 곡률 최적 라인 → 속도 프로파일·예상 랩타임 → 실제 폴 랩과 비교
2. **주행**: 같은 서킷 데이터를 브라우저 게임에 로드 → 키보드 또는 폰으로 주행 → 최적 라인/폴 랩 고스트와 경쟁
3. **비교**: 내 주행 라인 vs 최적 라인 vs 실제 폴 랩을 코너별로 비교

1단계에서 만든 **서킷 데이터(JSON)와 차량 물리 파라미터를 2·3단계가 그대로 재사용**하는 것이 설계의 핵심이다.

## 2. 단계 정의 (반드시 순서대로, 단계마다 동작하는 상태로 커밋)

각 단계는 그 자체로 실행 가능한 결과물이어야 한다. 다음 단계로 넘어가기 전에 "완료 기준"을 모두 충족한다.

### Stage 1 — 최적 라인 분석 앱
- `apexline/track.py`: FastF1에서 위치 데이터 로드, 중심선 추출, 트랙 폭 설정
- `apexline/optimizer.py`: 최소 곡률 최적 라인 계산
- `apexline/vehicle.py`: 차량 파라미터, 마찰원 기반 속도 프로파일, 랩타임 계산
- `apexline/export.py`: 서킷 JSON 내보내기 (스키마는 5절)
- `apps/analysis_app.py`: Streamlit 앱. 사용자가 서킷·연도·드라이버·트랙 폭·최대 횡/종가속도를 조절하고 결과를 지도/그래프로 확인
- 완료 기준: `streamlit run apps/analysis_app.py`로 실행되고, 최소 3개 서킷에서 최적 라인·속도·랩타임·폴 랩 비교가 표시됨. `data/tracks/`에 데모용 서킷 JSON 3개 이상 생성.

### Stage 2 — PC 주행 게임 (키보드)
- `server/main.py`: FastAPI 앱. 정적 파일 서빙, 서킷 JSON 제공 API
- `web/game/`: HTML Canvas + JavaScript 게임
  - 서킷 렌더링(트랙 경계, 중심선), 카메라 추적
  - 차량 물리: Stage 1의 `vehicle.py`와 같은 파라미터 사용(마찰 한계, 최고속도, 가감속 한계), 트랙 이탈 시 감속
  - 랩 타이머, 섹터 기록
  - 고스트: 최적 라인 차, 실제 폴 랩 차
- 완료 기준: 키보드(방향키/WASD)로 한 바퀴를 완주하고 랩타임이 기록됨.

### Stage 3 — 폰 컨트롤러
- `web/controller/`: 폰용 컨트롤러 페이지
  - 기울기(DeviceOrientation)로 조향, 화면 터치 영역으로 가속/브레이크
  - iOS는 버튼 탭 이벤트 안에서 `DeviceOrientationEvent.requestPermission()` 호출
  - 가로/세로 화면 방향에 따라 조향 축(beta/gamma)을 보정
- `server/`: WebSocket 엔드포인트로 폰 입력을 게임 화면에 중계. 방(room) 코드로 PC 화면과 폰을 짝지음
- PC 게임 화면에 컨트롤러 접속용 QR 코드 표시
- 완료 기준: 같은 네트워크의 폰으로 QR 접속 후 조작 가능. 폰이 없을 때 키보드 조작은 계속 동작.

### Stage 4 — 비교 분석 + 문서화
- 주행 기록(위치·속도·시간) 저장, 내 라인 vs 최적 라인 vs 폴 랩 비교 화면, 코너별 시간 손실 표시
- README 완성: 스크린샷, 데모 GIF/영상, 설치·실행법, 동작 원리, 한계, 레퍼런스
- 완료 기준: 처음 보는 사람이 README만 보고 설치·실행할 수 있음.

### Bonus (여유가 있을 때만)
2인 대결(폰 2대), 랩타임 리더보드, 강화학습 에이전트와 대결

## 3. 기술 스택
- Python 3.10+
- 분석: FastF1, NumPy, SciPy, pandas, Matplotlib, Plotly
- 최적화: SciPy 또는 CVXPY (최소 곡률 QP)
- 분석 UI: Streamlit
- 서버: FastAPI + Uvicorn (WebSocket)
- 게임/컨트롤러: 순수 HTML/CSS/JavaScript (Canvas). 빌드 도구·프레임워크 없이 동작하게 유지
- 테스트: pytest

## 4. 디렉터리 구조 (목표)
```
apex-line/
├── CLAUDE.md
├── README.md
├── LICENSE
├── requirements.txt
├── docs/
│   ├── PLAN.md              # 일정·체크리스트
│   └── images/              # README용 스크린샷, GIF
├── apexline/                # 핵심 Python 패키지
│   ├── __init__.py
│   ├── track.py
│   ├── optimizer.py
│   ├── vehicle.py
│   └── export.py
├── apps/
│   └── analysis_app.py      # Streamlit 분석 앱
├── server/
│   └── main.py              # FastAPI (정적 서빙 + API + WebSocket)
├── web/
│   ├── game/                # PC 게임 화면
│   └── controller/          # 폰 컨트롤러
├── scripts/
│   └── build_tracks.py      # 데모 서킷 JSON 일괄 생성
├── data/tracks/             # 생성된 서킷 JSON (데모용 소수만 커밋)
└── tests/
```

## 5. 서킷 JSON 스키마 (모든 단계의 공통 계약)

`data/tracks/<year>_<event_slug>.json`. 좌표 단위는 미터(m), 거리 기준으로 일정 간격 재샘플링한 닫힌 루프(마지막 점이 첫 점과 중복되지 않음).

```json
{
  "schema_version": 1,
  "meta": {"year": 2024, "event": "Italian Grand Prix", "circuit": "Monza", "session": "Q"},
  "track": {
    "centerline": [[x, y], ...],
    "width_left": [w, ...],
    "width_right": [w, ...],
    "length_m": 5793.0
  },
  "vehicle": {"mass_kg": 798, "a_lat_max": 45.0, "a_long_accel_max": 12.0, "a_long_brake_max": 50.0, "v_max": 95.0},
  "optimal": {"line": [[x, y], ...], "speed": [v, ...], "lap_time_s": 79.1},
  "pole_lap": {"driver": "LEC", "lap_time_s": 79.3, "line": [[x, y], ...], "speed": [v, ...], "time": [t, ...]}
}
```
스키마를 바꿔야 하면 `schema_version`을 올리고 이 절을 함께 수정한다.

## 6. 구현 메모
- FastF1: `fastf1.Cache.enable_cache("cache/")`를 항상 켠다. 위치 데이터 X, Y는 1/10 m 단위이므로 미터로 변환한다.
- 중심선: 여러 랩의 위치 데이터를 거리 기준으로 재샘플링 → 평균 → `scipy.interpolate.splprep(per=True)`로 매끄럽게.
- 트랙 폭: FastF1에는 트랙 경계가 없다. 기본값은 좌우 각 6 m 가정, 사용자가 조절 가능. 이 가정은 README "한계"에 명시한다.
- 최소 곡률: 중심선 법선 방향 오프셋 α를 변수로 하는 QP. 제약은 `-w_right + margin ≤ α ≤ w_left - margin`. 참고 문헌은 Heilmeier et al. (2019).
- 속도 프로파일: 곡률로 지점별 최대 코너 속도 → 앞으로(가속 한계) / 뒤로(제동 한계) 전파 → 마찰원으로 횡·종가속 결합.
- 게임 물리는 Python 쪽과 같은 파라미터를 JSON의 `vehicle`에서 읽어 사용한다.
- WebSocket 입력 메시지: `{"type": "input", "steer": -1.0~1.0, "throttle": 0.0~1.0, "brake": 0.0~1.0, "t": <ms>}`, 30~60 Hz.
- 폰 센서는 HTTPS(보안 컨텍스트)에서만 동작한다. 개발 시 `mkcert`로 로컬 인증서를 쓰거나 `cloudflared` 터널을 사용한다. 학교 Wi-Fi는 기기 간 통신을 막을 수 있으므로 핫스팟 사용 방법도 README에 적는다.

## 7. 작업 규칙
- **한 번에 한 단계씩** 작업한다. 사용자가 요청한 단계 범위를 넘지 않는다.
- 기능 단위로 작게 커밋한다. 커밋 메시지는 `[stage1] feat: ...`, `[stage2] fix: ...` 형식.
- 각 단계가 끝날 때 `docs/PLAN.md` 체크리스트와 README 해당 부분을 갱신한다.
- **외부 코드 출처 규칙(매우 중요)**: 다른 오픈소스·블로그·논문 코드를 참고하거나 가져오면 즉시 README의 "References"에 출처(URL, 라이선스)를 추가하고, 해당 파일 상단 주석에도 남긴다. GPL 등 MIT와 호환되지 않을 수 있는 라이선스의 코드는 가져오지 않는다. 출처 미표기는 과제에서 F 학점 사유다.
- FastF1 원본 데이터와 `cache/` 폴더는 커밋하지 않는다. `data/tracks/`의 데모 JSON 몇 개만 커밋한다.
- 폰이 없어도 프로젝트가 동작해야 한다. 키보드 조작을 항상 유지한다.
- 새 의존성을 추가하면 `requirements.txt`도 함께 수정한다.
- 핵심 계산 함수(`track`, `optimizer`, `vehicle`)에는 pytest 테스트를 작성한다.
- 코드 주석과 docstring은 한국어 또는 영어, 일관되게.

## 8. 자주 쓰는 명령
```bash
pip install -r requirements.txt
python scripts/build_tracks.py            # 데모 서킷 JSON 생성 (Stage 1)
streamlit run apps/analysis_app.py        # 분석 앱 (Stage 1)
uvicorn server.main:app --reload          # 게임 서버 (Stage 2~)
pytest                                    # 테스트
pytest tests/test_track.py::test_name     # 단일 테스트 실행
```
