# typepack-social

소셜 미디어 엔티티 타입팩 — VINEYARD Type Pack (`vineyard:typepack`).

**플랫폼 중립**입니다. X/Twitter 전용이 아니라 모든 엔티티에 `platform` 판별자 필드를 두고
Mastodon·Threads·Bluesky 등을 같은 타입으로 담습니다. 플랫폼별 팩을 따로 만들면 "이 계정과
저 계정이 같은 사람"을 물어볼 공통 타입이 사라집니다.

## 타입 (category: `social`)

| 타입 | label | icon | 정체성 | 설명 |
|---|---|---|---|---|
| `social.account` | `username` | at-sign | `platform`+`username` | 플랫폼 상의 계정 |
| `social.post` | `post_id` | message-circle | `platform`+`post_id` | 공개 게시글 |
| `social.hashtag` | `hashtag` | hash | `platform`+`hashtag` | 캠페인/트렌드 군집화 피벗 |
| `social.group` | `name` | users | `platform`+`group_id` | 커뮤니티/채널/그룹 |
| `social.media` | `media_url` | image | `media_url` | 게시글 첨부 미디어 |

## 엣지 (category: `social`)

- `posted_by` — `social.account → social.post`
- `replies_to` / `reposted` / `quoted` — `social.post → social.post` (스레드·확산 체인)
- `mentions` — `social.post → social.account`
- `follows` — `social.account → social.account`
- `uses_hashtag` — `social.post → social.hashtag`
- `member_of` / `posted_in` — 그룹 소속 / 그룹 게시
- `contains_media` — `social.post → social.media`

## 설계 메모

- **귀속은 이 팩이 소유하지 않습니다.** 계정 ↔ 사람/조직 연결은 Identity 팩의
  `identity.controls`가 담당하고, 이 팩은 **플랫폼 안에서 일어나는 일**만 다룹니다.
  `identity.account`와 겹쳐 보이지만 역할이 다릅니다 — 전자는 크로스플랫폼 페르소나
  귀속용 얇은 노드, `social.account`는 팔로워/바이오/게시물 수까지 담는 플랫폼 측 프로필입니다.
- **`social.media`의 정체성은 URL 하나뿐**입니다(`platform` 미포함). 같은 자산이 여러 게시글에
  나타나도 한 노드로 모여야 재게시 추적이 됩니다.
- **`media_type`은 닫힌 enum**(`image`/`video`/`gif`/`audio`/`unknown`)입니다. 수집 플러그인은
  플랫폼 고유 표기(X의 `photo`, `animated_gif` 등)를 여기에 **매핑해서** 넣어야 하며, 모르는
  값은 새 enum 멤버를 만들지 말고 `unknown`으로 보냅니다.
- **MAJOR 버전**: 타입 제거/정체성 변경 시에만. 프로퍼티·엣지 추가는 minor(additive).

## 검증

```bash
python3 -m pip install jsonschema
python3 -c "import json, jsonschema; jsonschema.validate(json.load(open('typepacks/social.json')), json.load(open('../registry/schemas/typepack.schema.json')))"
```

## 라이선스

Apache-2.0.
