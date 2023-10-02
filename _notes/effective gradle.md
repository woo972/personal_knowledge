---
tags: gradle, dev
---
>[!note]
>아래 코드 예시는 gradle 구 버전이므로, 재조사 필요함

## 'apply'
- apply 명령을 통해 외부 플러그인뿐만 아니라 내부 스크립트 파일을 적용할 수 있다.
```
...
apply from: "gradle/sonar.gradle"  // gradle 디렉토리 하위에 있는 sonar.gradle 파일을 읽어온다
...
```
## 'parallel'
- `--parallel` 옵션을 통해 병렬 빌드를 수행할 수 있다. 프로젝트를 분리하여야 하며, 공유 상태를 직접 수정하는 경우가 없어야 한다.
- cpu 코어를 활용해 테스트를 병렬로 실행할 수 있다
```
tasks.withType(Test) {
	maxParallelForks = 4
	// maxParalleslForks = Runtime.runtime.availableProcessors().intdiv(2) ?: 1 <- cpu 수에 맞춰서 fork 숫자를 정할 수 있다
}
```

## 'profile'
- `--profile` 옵션을 사용하여 벤치마크를 측정할 수 있다
