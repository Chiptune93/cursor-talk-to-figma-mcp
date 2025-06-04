# Cursor Talk to Figma MCP

이 프로젝트는 Cursor AI와 Figma 간의 Model Context Protocol(MCP) 통합을 구현하여, Cursor가 Figma의 디자인을 읽고 프로그래밍적으로 수정할 수 있도록 합니다.

https://github.com/user-attachments/assets/129a14d2-ed73-470f-9a4c-2240b2a4885c

## 프로젝트 구조

- `src/talk_to_figma_mcp/` - Figma 통합을 위한 TypeScript MCP 서버
- `src/cursor_mcp_plugin/` - Cursor와 통신하는 Figma 플러그인
- `src/socket.ts` - MCP 서버와 Figma 플러그인 간의 통신을 중계하는 WebSocket 서버

## 시작하기

1. Bun이 설치되어 있지 않다면 설치하세요:

```bash
curl -fsSL https://bun.sh/install | bash
```

2. 셋업을 실행하세요. 이 과정에서 MCP가 Cursor의 활성 프로젝트에 설치됩니다.

```bash
bun setup
```

3. Websocket 서버를 시작하세요

```bash
bun socket
```

4. MCP 서버 실행

```bash
bunx cursor-talk-to-figma-mcp
```

5. **신규** [Figma 커뮤니티 페이지](https://www.figma.com/community/plugin/1485687494525374295/cursor-talk-to-figma-mcp-plugin)에서 Figma 플러그인을 설치하거나 [로컬에서 설치](#figma-plugin)하세요.

## 빠른 동영상 튜토리얼

[동영상 링크](https://www.linkedin.com/posts/sonnylazuardi_just-wanted-to-share-my-latest-experiment-activity-7307821553654657024-yrh8)

## 디자인 자동화 예시

**대량 텍스트 내용 교체**

[@dusskapark](https://github.com/dusskapark)님이 대량 텍스트 교체 기능을 기여해주셨습니다. [데모 영상](https://www.youtube.com/watch?v=j05gGT3xfCs)을 참고하세요.

**인스턴스 오버라이드 전파**
[@dusskapark](https://github.com/dusskapark)님의 또 다른 기여입니다.
소스 인스턴스에서 여러 대상 인스턴스로 컴포넌트 인스턴스 오버라이드를 한 번에 전파할 수 있습니다. 이 기능은 유사한 커스터마이징이 필요한 컴포넌트 인스턴스 작업 시 반복적인 디자인 작업을 크게 줄여줍니다. [데모 영상](https://youtu.be/uvuT8LByroI)을 확인하세요.

## 수동 설치 및 설정

### MCP 서버: Cursor와 통합

`~/.cursor/mcp.json`의 Cursor MCP 설정에 서버를 추가하세요:

```json
{
  "mcpServers": {
    "TalkToFigma": {
      "command": "bunx",
      "args": ["cursor-talk-to-figma-mcp@latest"]
    }
  }
}
```

### WebSocket 서버

WebSocket 서버를 시작하세요:

```bash
bun socket
```

### Figma 플러그인

1. Figma에서 Plugins > Development > New Plugin으로 이동
2. "Link existing plugin" 선택
3. `src/cursor_mcp_plugin/manifest.json` 파일 선택
4. 플러그인이 Figma 개발 플러그인 목록에 나타납니다

## Windows + WSL 가이드

1. powershell로 bun 설치

```bash
powershell -c "irm bun.sh/install.ps1|iex"
```

2. `src/socket.ts`에서 hostname `0.0.0.0` 주석 해제

```typescript
// windows wsl에서 연결을 허용하려면 이 부분의 주석을 해제하세요
hostname: "0.0.0.0",
```

3. websocket 시작

```bash
bun socket
```

## 사용법

1. WebSocket 서버를 시작하세요
2. Cursor에 MCP 서버를 설치하세요
3. Figma를 열고 Cursor MCP 플러그인을 실행하세요
4. 플러그인에서 `join_channel`로 채널에 참여하여 WebSocket 서버에 연결하세요
5. Cursor에서 MCP 도구를 사용해 Figma와 통신하세요

## MCP 도구

MCP 서버는 Figma와 상호작용할 수 있는 다음 도구들을 제공합니다:

### 문서 & 선택

- `get_document_info` - 현재 Figma 문서 정보 가져오기
- `get_selection` - 현재 선택 정보 가져오기
- `read_my_design` - 파라미터 없이 현재 선택의 상세 노드 정보 가져오기
- `get_node_info` - 특정 노드의 상세 정보 가져오기
- `get_nodes_info` - 여러 노드 ID 배열로 상세 정보 가져오기

### 주석

- `get_annotations` - 현재 문서 또는 특정 노드의 모든 주석 가져오기
- `set_annotation` - 마크다운 지원으로 주석 생성 또는 업데이트
- `set_multiple_annotations` - 여러 주석을 효율적으로 일괄 생성/업데이트
- `scan_nodes_by_types` - 특정 타입의 노드 스캔(주석 대상 찾기에 유용)

### 프로토타이핑 & 연결

- `get_reactions` - 노드의 모든 프로토타입 리액션 가져오기(시각적 하이라이트 애니메이션 포함)
- `set_default_connector` - FigJam 커넥터 복사본을 기본 커넥터 스타일로 설정(연결 생성 전 필수)
- `create_connections` - 프로토타입 플로우 또는 커스텀 매핑 기반으로 노드 간 FigJam 커넥터 라인 생성

### 요소 생성

- `create_rectangle` - 위치, 크기, 선택적 이름으로 새 사각형 생성
- `create_frame` - 위치, 크기, 선택적 이름으로 새 프레임 생성
- `create_text` - 폰트 속성 커스터마이즈가 가능한 새 텍스트 노드 생성

### 텍스트 내용 수정

- `scan_text_nodes` - 대형 디자인에서 지능적으로 텍스트 노드 스캔 및 청킹
- `set_text_content` - 단일 텍스트 노드의 텍스트 내용 설정
- `set_multiple_text_contents` - 여러 텍스트 노드를 효율적으로 일괄 업데이트

### 오토 레이아웃 & 간격

- `set_layout_mode` - 프레임의 레이아웃 모드 및 래핑 동작 설정(NONE, HORIZONTAL, VERTICAL)
- `set_padding` - 오토 레이아웃 프레임의 패딩 값 설정(상, 우, 하, 좌)
- `set_axis_align` - 오토 레이아웃 프레임의 주축/보조축 정렬 설정
- `set_layout_sizing` - 오토 레이아웃 프레임의 가로/세로 크기 모드 설정(FIXED, HUG, FILL)
- `set_item_spacing` - 오토 레이아웃 프레임 내 자식 간 거리 설정

### 스타일링

- `set_fill_color` - 노드의 채우기 색상(RGBA) 설정
- `set_stroke_color` - 노드의 테두리 색상 및 두께 설정
- `set_corner_radius` - 노드의 모서리 반경 설정(코너별 개별 설정 가능)

### 레이아웃 & 정리

- `move_node` - 노드를 새 위치로 이동
- `resize_node` - 노드 크기 조정
- `delete_node` - 노드 삭제
- `delete_multiple_nodes` - 여러 노드를 한 번에 효율적으로 삭제
- `clone_node` - 기존 노드를 복사하여 새 위치에 생성(옵션: 위치 오프셋)

### 컴포넌트 & 스타일

- `get_styles` - 로컬 스타일 정보 가져오기
- `get_local_components` - 로컬 컴포넌트 정보 가져오기
- `create_component_instance` - 컴포넌트 인스턴스 생성
- `get_instance_overrides` - 선택한 컴포넌트 인스턴스의 오버라이드 속성 추출
- `set_instance_overrides` - 추출한 오버라이드를 대상 인스턴스에 적용

### 내보내기 & 고급

- `export_node_as_image` - 노드를 이미지(PNG, JPG, SVG, PDF)로 내보내기(현재는 base64 텍스트로 제한 지원)

### 연결 관리

- `join_channel` - Figma와 통신할 특정 채널에 참여

### MCP 프롬프트

MCP 서버는 복잡한 디자인 작업을 안내하는 여러 헬퍼 프롬프트를 포함합니다:

- `design_strategy` - Figma 디자인 작업을 위한 모범 사례
- `read_design_strategy` - Figma 디자인 읽기 모범 사례
- `text_replacement_strategy` - Figma 디자인에서 텍스트 교체를 위한 체계적 접근법
- `annotation_conversion_strategy` - 수동 주석을 Figma 네이티브 주석으로 변환하는 전략
- `swap_overrides_instances` - Figma에서 컴포넌트 인스턴스 간 오버라이드 전환 전략
- `reaction_to_connector_strategy` - 'get_reactions' 결과를 활용해 Figma 프로토타입 리액션을 커넥터 라인으로 변환하고, 'create_connections'를 순차적으로 사용하는 전략

## 개발

### Figma 플러그인 빌드

1. Figma 플러그인 디렉터리로 이동:

   ```
   cd src/cursor_mcp_plugin
   ```

2. code.js와 ui.html을 수정하세요

## 모범 사례

Figma MCP 사용 시:

1. 명령 전 항상 채널에 참여하세요
2. 먼저 `get_document_info`로 문서 개요를 확인하세요
3. 수정 전 `get_selection`으로 현재 선택을 확인하세요
4. 필요에 따라 적절한 생성 도구를 사용하세요:
   - 컨테이너는 `create_frame`
   - 기본 도형은 `create_rectangle`
   - 텍스트 요소는 `create_text`
5. `get_node_info`로 변경 사항을 확인하세요
6. 일관성을 위해 컴포넌트 인스턴스를 적극 활용하세요
7. 모든 명령은 예외를 발생시킬 수 있으므로 오류 처리를 적절히 하세요
8. 대형 디자인의 경우:
   - `scan_text_nodes`의 청킹 파라미터를 활용하세요
   - WebSocket 업데이트로 진행 상황을 모니터링하세요
   - 적절한 오류 처리를 구현하세요
9. 텍스트 작업 시:
   - 가능하면 일괄 작업을 사용하세요
   - 구조적 관계를 고려하세요
   - 타겟 내보내기로 변경 사항을 검증하세요
10. 레거시 주석 변환 시:
    - 텍스트 노드를 스캔해 번호 마커와 설명을 식별하세요
    - `scan_nodes_by_types`로 주석 대상 UI 요소를 찾으세요
    - 경로, 이름, 근접성으로 마커와 대상 요소를 매칭하세요
    - `get_annotations`로 주석을 적절히 분류하세요
    - `set_multiple_annotations`로 네이티브 주석을 일괄 생성하세요
    - 모든 주석이 올바르게 대상에 연결되었는지 확인하세요
    - 변환 완료 후 레거시 주석 노드를 삭제하세요
11. 프로토타입 누들을 FigJam 커넥터로 시각화:

- `get_reactions`로 프로토타입 플로우 추출,
- `set_default_connector`로 기본 커넥터 설정,
- `create_connections`로 커넥터 라인 생성하여 시각적 플로우 맵을 만드세요.

## 라이선스

MIT
