# How to use

## Auto fetch from mirror

이 workflow는 GitLab 미러 저장소에서 최신 변경 사항을 가져오고, 이를 GitHub 저장소로 푸시하는 과정을 자동화합니다.

주로 GitLab 저장소를 GitHub 저장소와 자동으로 동기화하고 싶을 때 유용하게 사용할 수 있습니다.
매일 정해진 시간에 자동 실행하거나, 필요할 때 수동으로 실행할 수도 있습니다.

### 워크플로 트리거

이 워크플로는 다음 두 가지 방법으로 실행할 수 있습니다:

- 스케줄 트리거: 매일 UTC 기준 15시에 자동 실행됩니다. (cron 표현식: `0 15 * * *`)
- 수동 트리거: Actions에서 workflow_dispatch를 통해 직접 실행할 수 있습니다.

### 워크플로 단계

**GitLab 저장소 미러 클론**

GitLab 저장소를 `--mirror` 옵션으로 클론합니다.
이 방식은 브랜치, 태그 등 모든 참조(ref)를 복제하여 저장소의 전체 상태를 그대로 가져옵니다.
GitHub Secrets에 설정된 GitLab 아이디와 비밀번호(또는 토큰) 를 사용하여 인증합니다.

**가져오기 및 가지치기(Fetch and prune)**

클론된 저장소에서 최신 변경사항을 가져오고(fetch), 삭제된 참조(ref)를 정리(prune)합니다.
클론된 저장소 디렉토리 안에서 실행됩니다.

**대상 GitHub 저장소로 푸시**

GitHub 저장소로 mirror push를 수행하여 모든 브랜치, 태그, 참조를 동기화합니다.
GitHub Secrets에 설정된 Personal Access Token (PAT) 을 사용하여 인증합니다.

### 필요한 GitHub Secrets

이 워크플로를 사용하기 전에 GitHub 저장소에 다음 Secrets를 설정해야 합니다:

| Secret 이름            | 설명                                                         |
|-------------------------|--------------------------------------------------------------|
| `GITLAB_USERNAME`        | GitLab 계정 사용자명 (HTTPS 인증용)                           |
| `GITLAB_PASSWORD`        | GitLab 계정 비밀번호 또는 액세스 토큰                         |
| `GITLAB_REPO_URL`        | GitLab 저장소의 URL (`.git` 확장자 제외)                      |
| `GITLAB_REPO_NAME`       | GitLab 저장소 이름 (로컬 폴더명으로 사용)                      |
| `PAT_TOKEN`              | GitHub Personal Access Token (저장소에 푸시할 수 있는 권한 필요) |
| `TARGET_REPO`            | 대상 GitHub 저장소 이름 (`owner/repository` 형식)             |

### 주의사항

- GitLab 저장소는 HTTPS로 접근 가능해야 합니다.
- GitHub Personal Access Token은 최소한 repo 권한이 있어야 합니다.
- `git clone --mirror`와 `git push --mirror`를 사용하므로 모든 브랜치, 태그, 참조(ref) 가 정확하게 동기화됩니다.
- Mirror push는 GitHub 저장소의 참조를 덮어쓸 수 있으므로 주의가 필요합니다.
