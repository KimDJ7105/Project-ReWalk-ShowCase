> 이 레포는 한국공학대학교 게임공학과 졸업작품 'Project: Re:Walk'의 실제 개발 레포에서, 개인 정보 및 프로젝트와 무관한 자료를 제외하고 정리한 버전입니다. 팀 구성: 김동재(팀장·서버), 양정우(클라이언트), 임윤수(기획).

# Project: Re:Walk

2024년도 한국공학대학교 게임공학과 졸업작품

## 개요

| 항목 | 내용 |
|---|---|
| 팀명 | KYK |
| 팀원 | 김동재 (팀장·서버), 양정우 (클라이언트), 임윤수 (기획·모델링) |
| 지도교수 | 정내훈 교수님 |
| 개발 기간 | 2023.11 ~ 2024.08 |
| 장르 | 팀 기반 슈팅 게임 |
| 기술 스택 | C++, Boost.Asio, DirectX 12 |
| 시연 영상 | https://youtu.be/5hCu5S4Mi8k?si=sD0eD_0LJQf-hg1x |

## 담당 역할 (김동재)

팀장 겸 서버 개발 전담. Boost.Asio 기반 비동기 멀티스레드 서버 설계 및 구현 (`ReWorkServer/`, `Lobby/`).

## 기술적 도전

- **Boost.Asio 신규 학습**: 기존에 학습·사용해온 IOCP 대신 Boost.Asio를 새로 학습해 프로젝트에 적용
- **스레드 아키텍처**: 스레드별로 게임 룸을 배타적으로 관리하는 구조 설계 — 룸 단위로 스레드를 나눠 룸 간 데이터 경합을 원천적으로 차단
- 스트레스 테스트 기준 최대 동시 접속 약 2,000명 처리 확인

## 문제 해결 사례 — 비동기 콜백과 객체 생명주기

**문제**: 플레이어 로그아웃 시 간헐적으로 서버가 크래시. 발생 시점이 일정하지 않아 단순 로직 오류로 보기 어려웠음.

**원인 분석**: 로그를 추가하고 상황을 반복 재현하며 추적한 결과, 로그아웃 처리 과정에서 `SESSION` 객체가 먼저 파괴된 뒤 그 객체를 참조하던 비동기 콜백이 실행되면서 dangling reference가 발생하는 것이 원인이었음. `GAME`은 게임방과 그 안의 플레이어들(`SESSION`)을 관리하는 객체이고, `SESSION`은 각 클라이언트의 연결·상태를 관리하는 객체인데, 이 둘 사이의 소유 관계와 비동기 작업의 수명이 얽혀 있었음.

**해결**: 객체의 소유 관계와 비동기 작업의 수명을 명확히 분리.

- `GAME`이 `shared_ptr<SESSION>`으로 세션을 소유 (`game.h`의 `ingame_player`)
- `SESSION`이 자신을 소유한 `GAME`/`SERVER`를 참조할 때는 `weak_ptr`을 사용해 순환 참조 방지 (`session.h`의 `my_game`, `my_server`)
- `SESSION`을 `enable_shared_from_this`로 구성하고, `do_read()`/`do_write()`에서 `shared_from_this()`로 자기 자신의 `shared_ptr`을 비동기 콜백 람다에 캡처 — 비동기 작업이 완료될 때까지 `SESSION`의 수명을 보장
- 콜백 내부에서는 `weak_ptr::lock()`으로 `GAME`의 생존 여부를 확인한 뒤 접근하도록 구성해, 상대 객체가 먼저 소멸한 경우에도 안전하게 처리

**결과**: 로그아웃 시 발생하던 간헐적 크래시 제거. 동시성 환경의 문제는 오류가 발생한 코드만 고쳐서는 해결되지 않으며, 객체의 소유권과 비동기 작업의 실행 시점을 함께 설계해야 한다는 것을 배움.

## 실행 방법

1. `ReWorkServer.exe` 실행 후 IP 입력
2. `Lobby.exe` 실행
3. 클라이언트 실행

## 개발 환경 설정

### 1. 프로젝트 열기

리포지토리를 복사한 뒤, 클라이언트(`Game/Game.sln`)와 서버(`ReWorkServer/`) 프로젝트를 각각 엽니다.

### 2. NuGet 패키지 복원

`도구 → NuGet 패키지 관리 → 패키지 관리자 → 업데이트 → 모든 패키지 선택` 후 업데이트 (Boost 1.85.0 확인)

### 3. FBX SDK 설치

1. [Autodesk FBX SDK 2020.3](https://www.autodesk.com/developer-network/platform-technologies/fbx-sdk-2020-3)에서 **FBX SDK 2020.3.2 VS2019** (exe, 약 156MB) 다운로드 후 실행
2. 기본 설치 경로 기준 `C:\Program Files\Autodesk\FBX\FBX SDK\2020.3.2` 안에 `include`, `lib` 폴더가 생성됨
3. `Game\Library\Include` 폴더 안에 `FBX` 폴더를 만들고, 위에서 설치된 `include` 폴더의 내용물을 그대로 복사
4. `Game\Library\Lib` 폴더 안에도 `FBX` 폴더를 만들고, 설치된 `lib\x64`의 `debug`, `release` 폴더를 그대로 복사
