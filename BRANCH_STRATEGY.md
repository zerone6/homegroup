# Git 브랜치 전략

## 개요

이 프로젝트는 메인 저장소(homegroup)와 3개의 서브모듈로 구성되어 있으며, 일관된 브랜치 전략을 사용합니다.

### 저장소 구조
- **메인 저장소**: homegroup
- **서브모듈**:
  - highschool-calendar
  - landing-page
  - my-realestate-calc

## 브랜치 구조

### 브랜치 종류

```
master (production)
  ↑
dev (development)
  ↑
feature/* (feature branches)
```

#### master 브랜치
- 프로덕션 환경에 배포되는 안정적인 코드
- master 브랜치에 푸시 시 자동 배포 실행
- 직접 푸시 금지, dev 브랜치에서 PR 또는 머지를 통해서만 업데이트

#### dev 브랜치
- 개발 및 통합 브랜치
- 모든 개발 작업의 기본 브랜치
- 서브모듈의 dev 브랜치를 참조

#### feature/* 브랜치
- 새로운 기능 개발을 위한 브랜치
- dev 브랜치에서 분기하여 작업 후 dev로 병합
- 명명 규칙: `feature/기능명` (예: `feature/user-authentication`)

## 서브모듈 브랜치 전략

각 서브모듈도 동일한 브랜치 구조를 가집니다:
- **master**: 프로덕션 버전
- **dev**: 개발 버전

### .gitmodules 설정

```ini
[submodule "highschool-calendar"]
    path = highschool-calendar
    url = https://github.com/zerone6/highschool-calendar.git
    branch = dev

[submodule "my-realestate-calc"]
    path = my-realestate-calc
    url = https://github.com/zerone6/my-realestate-calc
    branch = dev

[submodule "landing-page"]
    path = landing-page
    url = https://github.com/zerone6/indexpage.git
    branch = dev
```

## 초기 설정

### 1. Homegroup에 dev 브랜치 생성

```bash
git checkout -b dev
git push -u origin dev
```

### 2. 각 서브모듈에 dev 브랜치 생성

```bash
# highschool-calendar
cd highschool-calendar
git checkout -b dev
git push -u origin dev
cd ..

# landing-page
cd landing-page
git checkout -b dev
git push -u origin dev
cd ..

# my-realestate-calc
cd my-realestate-calc
git checkout -b dev
git push -u origin dev
cd ..
```

### 3. .gitmodules 업데이트

.gitmodules 파일에 각 서브모듈의 `branch = dev` 설정 추가 후:

```bash
git add .gitmodules
git commit -m "chore: configure submodules to track dev branch"
git push origin dev
```

### 4. 서브모듈 설정 적용

```bash
git submodule update --remote --merge
```

## 일상적인 워크플로우

### 개발 작업 시작

```bash
# dev 브랜치로 전환
git checkout dev

# 서브모듈을 최신 dev 버전으로 업데이트
git submodule update --remote --merge

# 새로운 기능 브랜치 생성 (선택사항)
git checkout -b feature/new-feature
```

### 서브모듈 수정

```bash
# 1. 서브모듈 디렉토리로 이동
cd landing-page

# 2. dev 브랜치 확인
git checkout dev

# 3. 수정 작업 수행
# ... 코드 수정 ...

# 4. 커밋 및 푸시
git add .
git commit -m "feat: add new landing page feature"
git push origin dev

# 5. 메인 저장소로 돌아가기
cd ..

# 6. 메인 저장소에 서브모듈 업데이트 반영
git add landing-page
git commit -m "chore: update landing-page submodule"
git push origin dev
```

### 메인 저장소만 수정

```bash
# dev 브랜치에서 작업
git checkout dev

# 수정 작업 수행
# ... 코드 수정 ...

# 커밋 및 푸시
git add .
git commit -m "feat: add new feature"
git push origin dev
```

### 모든 서브모듈 일괄 업데이트

```bash
# dev 브랜치에서 모든 서브모듈 업데이트
git checkout dev
git submodule update --remote --merge

# 변경사항이 있으면 커밋
git add .
git commit -m "chore: update all submodules to latest dev"
git push origin dev
```

### 배포 (dev → master)

```bash
# 1. master 브랜치로 전환
git checkout master

# 2. 서브모듈도 master로 전환
git submodule foreach 'git checkout master'

# 3. dev 브랜치 머지
git merge dev

# 4. 서브모듈의 master 브랜치도 업데이트 (필요시)
# 각 서브모듈에서 dev를 master로 머지해야 함
cd highschool-calendar
git checkout master
git merge dev
git push origin master
cd ..

cd landing-page
git checkout master
git merge dev
git push origin master
cd ..

cd my-realestate-calc
git checkout master
git merge dev
git push origin master
cd ..

# 5. 메인 저장소 서브모듈 참조 업데이트
git add .
git commit -m "chore: update submodules to master"

# 6. master 푸시 (자동 배포 실행됨)
git push origin master
```

## 유용한 명령어

### 서브모듈 상태 확인

```bash
# 서브모듈 현재 커밋 및 브랜치 확인
git submodule status

# 서브모듈 상세 정보
git submodule foreach 'git branch -v'
```

### 서브모듈 업데이트

```bash
# 모든 서브모듈 dev 브랜치로 업데이트
git submodule update --remote --merge

# 특정 서브모듈만 업데이트
git submodule update --remote --merge landing-page
```

### 서브모듈 브랜치 일괄 전환

```bash
# 모든 서브모듈을 dev로 전환
git submodule foreach 'git checkout dev'

# 모든 서브모듈을 master로 전환
git submodule foreach 'git checkout master'
```

### 변경사항 확인

```bash
# 메인 저장소 및 서브모듈의 변경사항 확인
git status

# 서브모듈 내부 변경사항 확인
git submodule foreach 'git status'

# 서브모듈 diff 확인
git diff --submodule
```

## 주의사항

### 1. 서브모듈 커밋 순서
서브모듈을 수정한 경우, 반드시 다음 순서를 따라야 합니다:
1. 서브모듈 내부에서 커밋 및 푸시
2. 메인 저장소에서 서브모듈 참조 업데이트 커밋

### 2. master 브랜치 푸시 주의
master 브랜치에 푸시하면 자동 배포가 실행됩니다. 배포 전 충분한 테스트를 거쳐야 합니다.

### 3. 서브모듈 브랜치 불일치
homegroup의 dev 브랜치는 서브모듈의 dev 브랜치를, master는 서브모듈의 master를 참조하도록 관리합니다.

### 4. Detached HEAD 상태
서브모듈은 특정 커밋을 참조하므로 detached HEAD 상태가 될 수 있습니다. 수정 전 항상 올바른 브랜치로 전환하세요:

```bash
cd <submodule-directory>
git checkout dev
```

## Makefile 명령어 (선택사항)

편의를 위해 Makefile에 다음 명령어를 추가할 수 있습니다:

```makefile
.PHONY: update-dev
update-dev:
	git checkout dev
	git submodule update --remote --merge
	git add .
	git commit -m "chore: update submodules to latest dev" || true
	git push origin dev

.PHONY: switch-to-dev
switch-to-dev:
	git checkout dev
	git submodule foreach 'git checkout dev'
	git submodule update --remote --merge

.PHONY: switch-to-master
switch-to-master:
	git checkout master
	git submodule foreach 'git checkout master'

.PHONY: status-all
status-all:
	@echo "=== Main Repository ==="
	git status
	@echo "\n=== Submodules ==="
	git submodule foreach 'echo "\n--- $$name ---" && git status'
```

## GitHub 설정 권장사항

### 브랜치 보호 규칙

**master 브랜치:**
- Require pull request before merging
- Require approvals (최소 1명)
- Require status checks to pass

**dev 브랜치:**
- 직접 푸시 허용
- 선택적으로 PR 워크플로우 사용

### GitHub Actions

현재 설정: master 브랜치 푸시 시 자동 배포

향후 dev 브랜치용 CI/CD 파이프라인 추가 고려 (테스트, 빌드 검증 등)

## 참고 자료

- [Git Submodules 공식 문서](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [Git Flow 브랜치 전략](https://nvie.com/posts/a-successful-git-branching-model/)
