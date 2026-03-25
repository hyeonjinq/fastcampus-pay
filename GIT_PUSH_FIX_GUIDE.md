# Git 푸시 오류 해결 방법

## 1. 로컬에서 이미 해결한 것들
✅ AWS 액세스 키 제거 (DynamoDBAdapter.java)
✅ Vault 토큰 제거 (docker-compose.yaml)
✅ .gitignore 업데이트 (빌드 결과물 제외)

## 2. 필요한 추가 조치

### A. 깃 히스토리에서 secrets 제거 (GitHub 권장 방법)
1. 다음 URL에서 제시된 방법으로 이전 커밋들의 secrets 제거:
   https://docs.github.com/code-security/secret-scanning/working-with-secret-scanning-and-push-protection/working-with-push-protection-from-the-command-line#resolving-a-blocked-push

2. BFG Repo-Cleaner 사용 (권장):
   ```
   # Java 필요
   java -jar bfg-1.14.0.jar --delete-files secrets-to-remove.txt
   git reflog expire --expire=now --all && git gc --prune=now --aggressive
   ```

3. 또는 git filter-branch 사용:
   ```
   git filter-branch --tree-filter 'rm -f secrets-to-remove.txt' -- --all
   ```

### B. 대용량 JAR 파일 제거
```
# build 디렉토리의 JAR 파일 모두 제거
git rm -r --cached */build/
git rm -r --cached build/
git commit -m "Remove build artifacts"
```

### C. 최종 푸시
```
git push --force origin main
```

## 3. 주의사항
- `git push --force`를 사용하므로 다른 사람과 협업 중이면 사전 공지 필요
- 팀원들은 리포지토리를 다시 클론해야 함

## 4. 향후 예방
- .gitignore에 이미 */build/ 추가됨
- environment variables 또는 .env 파일 사용 권장
- pre-commit hook으로 secrets 스캔 설정 권장

