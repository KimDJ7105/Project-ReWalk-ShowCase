**2024년도 한국공학대학교 졸업작품 팀 KYK**
 - 팀원 소개
   - 김동재 : 팀장 및 서버 개발, Boost.ASIO를 활용한 비동기 멀티스레드 서버 설계/구현
   - 양정우 : 클라이언트 개발, DirectX 12를 기반으로한 클라이언트 제작
   - 임윤수 : 게임 기획 및 모델링
 - 지도교수 : 정내훈 교수님
 - 개발 기간 : 2023.11 ~ 2024.08
 - 장르 : 팀 기반 슈팅 게임
 - 사용 툴 : C++, DirectX 12, Boost.ASIO
 - Youtube Link : https://youtu.be/5hCu5S4Mi8k?si=sD0eD_0LJQf-hg1x

**기술적 도전**
 - 김동재
   1. Boost.ASIO : 기존에 학습하고 사용했던 익숙한 IOCP 대신, Boost.ASIO를 학습하고 프로젝트에 사용.
   2. 스레드 아키텍쳐 : 각 스레드가 각자 게임 룸을 관리하고, 각 룸에 배타성을 유지하도록 하는 구조 설계 및 개발

**문제 해결 사례**
 - 비동기 환경에서의 객체 유효성 : 비동기 처리 과정에서 발생하는 메모리 충돌 문제를 해결하기 위해 객체 생명 주기 조사. 로그와 재현 테스트를 통해
                                 이미 소멸된 객체에 접근하는 구조적 결함 발견. 스마트 포인터 도입을 통해 시스템 안정성 확보. 

//게임 실행 방법
1. ReWalkServer.exe 실행 후 ip입력
2. Lobby.exe 실행 
3. 클라이언트 실행

//개발 환경 설정
컴퓨터에 리포지토리 복사 후 클라이언트와 서버 프로젝트 열기 
도구 - nuget 패키지 - 패키지 관리자 - 업데이트 - 모든 패키지 선택 (boost 1.85.0 확인) - 업데이트 클릭

https://www.autodesk.com/developer-network/platform-technologies/fbx-sdk-2020-3
해당 링크에서 FBX SDK 2020.3.2 VS2019 (exe - 156196Kb)를 다운로드 후 실행.

다운로드 기본경로 "C:\Program Files\Autodesk\FBX\FBX SDK\2020.3.2"라고 한다면
해당 폴더 내에 include와 lib가 존재한다.

"KYK_pro_con\양정우(클라이언트)\DirectX12\Game\Library" 폴더 내에 Include폴더로 들어가서
FBX폴더를 생성하여 생성한 폴더 내에 위에서 다운로드한 include의 내용물을 붙여넣기한다.

다시 "KYK_pro_con\양정우(클라이언트)\DirectX12\Game\Library" 폴더 내에 Lib폴더로 들어가서
FBX폴더를 생성하고 해당 폴더 내에 위에서 다운로드한 lib폴더에x64내의 debug와 release를 폴더채로 붙여넣기한다.
