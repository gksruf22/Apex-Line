# Apex Line 🏎️

> 실제 F1 주행 데이터로 최적 레이싱 라인을 계산하고, 그 서킷 위를 스마트폰으로 직접 달려보는 오픈소스 프로젝트

![License](https://img.shields.io/badge/license-MIT-green)
![Python](https://img.shields.io/badge/python-3.10%2B-blue)

<!-- TODO: 대표 데모 GIF (docs/images/demo.gif) -->

## 소개

Apex Line은 세 가지를 합니다.

1. **분석**: 실제 F1 텔레메트리(FastF1)에서 서킷 형상을 추출하고, 최소 곡률 최적화와 차량 물리 모델로 최적 레이싱 라인, 구간별 속도, 예상 랩타임을 계산합니다. 사용자는 서킷과 파라미터를 바꿔가며 직접 분석할 수 있습니다.
2. **주행**: 같은 서킷을 브라우저 게임으로 불러와 키보드 또는 **스마트폰(기울여서 조향)** 으로 직접 운전합니다. 최적 라인과 실제 폴 포지션 랩이 고스트 차로 함께 달립니다.
3. **비교**: 내 주행 라인, 최적 라인, 실제 폴 랩을 겹쳐 보며 코너별로 어디서 시간을 잃었는지 확인합니다.

## 데모

| 분석 앱 | 주행 게임 | 폰 컨트롤러 |
|---|---|---|
| <!-- TODO --> | <!-- TODO --> | <!-- TODO --> |

## 주요 기능

- [ ] 서킷 중심선 추출 및 트랙 폭 설정
- [ ] 최소 곡률 최적 라인 계산
- [ ] 속도 프로파일 및 예상 랩타임 계산
- [ ] 실제 폴 포지션 랩과 비교
- [ ] Streamlit 분석 앱
- [ ] 브라우저 주행 게임 (키보드)
- [ ] 스마트폰 컨트롤러 (QR 접속, 기울기 조향)
- [ ] 주행 기록 비교 분석

## 설치

```bash
git clone https://github.com/gksruf22/Apex-Line.git
cd Apex-Line
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

## 사용법

### 1. 분석 앱
```bash
streamlit run apps/analysis_app.py
```

### 2. 주행 게임
```bash
uvicorn server.main:app --reload
```
브라우저에서 `http://localhost:8000`을 엽니다. 키보드(방향키 또는 WASD)로 조작합니다.

### 3. 폰으로 조작하기
<!-- TODO: HTTPS 설정(mkcert 또는 cloudflared), 같은 Wi-Fi/핫스팟, QR 접속, iOS 센서 권한 허용 방법 -->

## 동작 원리

<!-- TODO: 각 단계 구현 후 작성 -->
- **중심선 추출**:
- **최소 곡률 최적화**:
- **속도 프로파일 (마찰원 모델)**:
- **폰 ↔ 게임 통신 (WebSocket)**:

## 프로젝트 구조

```
apexline/   핵심 계산 패키지 (서킷, 최적화, 차량 모델)
apps/       Streamlit 분석 앱
server/     FastAPI 서버 (API, WebSocket)
web/        게임 화면, 폰 컨트롤러
scripts/    데모 서킷 데이터 생성
data/       생성된 서킷 JSON
tests/      테스트
docs/       계획, 이미지
```

## 한계

- FastF1은 트랙 경계를 제공하지 않으므로 트랙 폭은 가정값(기본 좌우 각 6 m)을 사용합니다.
- 차량 모델은 단순화된 포인트 질량 + 마찰원 모델로, 공기역학·타이어 온도·고저차 등은 반영하지 않습니다.
<!-- TODO: 추가 -->

## 데이터 출처 및 이용 안내

주행 데이터는 [FastF1](https://github.com/theOehrly/Fast-F1) 라이브러리를 통해 실행 시점에 내려받습니다. 데이터의 권리는 Formula 1 및 각 제공자에게 있으며, 이 저장소는 원본 데이터를 재배포하지 않습니다. 이 프로젝트는 Formula 1과 관련이 없는 비공식 교육용 프로젝트입니다.

## References

- FastF1 — https://github.com/theOehrly/Fast-F1 (MIT License)
- A. Heilmeier et al., "Minimum curvature trajectory planning and control for an autonomous race car," *Vehicle System Dynamics*, 2019.
<!-- 참고하거나 가져온 코드·자료는 반드시 여기에 출처와 라이선스를 추가 -->

## 개발 도구 안내

이 프로젝트는 AI 코딩 도구 [Claude Code](https://claude.com/claude-code)의 도움을 받아 개발했습니다. 설계, 검토, 테스트, 최종 결정은 작성자가 수행했습니다.

## License

[MIT License](LICENSE) © 2026 gksruf22
