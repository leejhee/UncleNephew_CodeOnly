# 삼촌 놀아줘! — Photon 멀티플레이 클라이언트 코드

2021년 팀 프로젝트 **삼촌 놀아줘!**의 코드 열람용 저장소입니다. Unity 기반 2인 멀티플레이 게임에서 서브 프로그래머로 참여해 **Photon 방 생성·입장·역할별 로비와 게임 진행 연결, 미니게임 1·4**를 담당했습니다.

원격 저장소 유실 이후 로컬에서 복구한 코드를 바탕으로 구성했습니다. 원래 개발 당시의 전체 커밋 이력을 보존한 저장소는 아닙니다.

## 플레이 진행

호스트가 방 이름을 정해 2인 방을 만들고, 게스트가 같은 이름을 입력해 입장합니다. 인원과 역할에 따라 대기 UI를 바꾸고, 호스트의 시작 요청을 RPC로 전달해 프롤로그와 미니게임 진행을 연결합니다.

여기서 입력하는 방 이름·번호는 `CreateRoom`과 `JoinRoom`에 사용하는 방 식별자입니다. 별도의 비밀번호 인증 시스템으로 구현한 것은 아닙니다.

## 주요 코드

| 영역 | 역할 | 코드 |
| --- | --- | --- |
| Photon 연결과 방 관리 | 2인 방 생성·입장·퇴장, 실패 콜백과 준비 상태 | [NetworkMgr.cs](Scripts/Mgrs/NetworkMgr.cs) |
| 역할별 로비 | 호스트·게스트 대기 상태와 게임 시작 요청 | [Room.cs](Scripts/Room/Room.cs), [Room 상태 코드](Scripts/Room) |
| 방 생성·입장 화면 | 입력 팝업과 역할별 UI | [InputRoomName.cs](Scripts/UI/Popup/InputRoomName.cs), [UncleUI](Scripts/UI/UncleUI), [NephewUI](Scripts/UI/NephewUI) |
| 씬 진행 | 씬 로딩과 화면 연결 | [SceneMgr.cs](Scripts/Mgrs/SceneMgr.cs) |
| 게임 초기화 | 공통 매니저 생성과 씬 전환 후 재구성 | [GameInstance.cs](Scripts/Core/GameInstance.cs) |
| 상태 전환 | 로비·미니게임에서 사용하는 상태 기반 구조 | [StateMachine.cs](Scripts/Core/StateMachine.cs) |
| 미니게임 1 | 각도·힘 선택, 턴 진행과 점수 공유 | [InGame1.cs](Scripts/InGameLogic/1/InGame1.cs), [게임 로직](Scripts/InGameLogic/1), [관련 UI](Scripts/UI/InGame1UI) |
| 미니게임 4 | 입력·시간 기록, 진행 상태와 결과 공유 | [InGame4.cs](Scripts/InGameLogic/4/InGame4.cs), [게임 로직](Scripts/InGameLogic/4), [관련 UI](Scripts/UI/InGame4UI) |

## 네트워크 처리 범위

- `RoomOptions.MaxPlayers = 2`로 방 인원을 제한합니다.
- 방 생성·입장 실패와 상대방 퇴장을 처리하고 관련 안내 UI를 연결합니다.
- RPC로 시작 요청, 미니게임 상태·점수·입력 및 기록값을 전달합니다.
- 플레이어 Custom Properties로 준비 및 승리 관련 상태를 공유합니다.

이 구현은 2인 게임 진행을 연결하기 위한 클라이언트 코드입니다. 전용 서버의 권위적 판정, 재접속 복구, 정밀한 네트워크 시계 동기화까지 구현했다는 의미는 아닙니다.

## 담당 범위와 복구본 안내

- 본인 담당인 로비·진행 연결과 미니게임 1·4를 중심으로 읽을 수 있도록 경로를 정리했습니다.
- 공통 시스템과 다른 미니게임을 참조하는 일부 UI·씬 연결 코드도 포함되어 있습니다. 이 때문에 전체 미니게임을 단독 구현했다고 주장하지 않습니다.
- 복구본의 코드 상태를 보존한 자료이며, 원래 개발 당시 모든 변경의 작성자와 시점을 현재 저장소 이력만으로 증명할 수는 없습니다.

## 실행 제한과 의존성

- 원본 `Assets/Scripts`에서 옮긴 소스이며, 현재 경로는 `Scripts`입니다.
- 이미지·음원·씬·프리팹·폰트·Photon 설정과 외부 에셋 본체는 포함하지 않았습니다.
- Photon PUN/Realtime, Unity UI, SerializableDictionary 관련 외부 의존성이 있습니다. Photon SDK와 프로젝트 설정, 관련 리소스가 없는 상태에서 바로 실행할 수는 없습니다.
- 완성된 Unity 프로젝트나 실행 파일의 배포가 아니라 담당 구현의 코드 검토를 위한 저장소입니다. 이번 문서 작성에서 실제 멀티플레이 실행 검증은 수행하지 않았습니다.
- 팀 코드와 외부 의존성의 이용 조건을 변경하는 별도 라이선스는 부여하지 않습니다.
