---
tags: dev, github/actions, devops, cicd
---
## Overview
- github에서 CI/CD를 지원하는 툴 
- public repo는 무료, privite repo는 월 2000분 무료
## Concept
- github actions의 작업은 workflow라고 불린다. workflow는 push, delete 등 repo에 대한 event가 발생할 때 트리거되며, 여러개의 workflow를 만들어서 사용할 수 있다. (빌드용, 패키징용 등)	
- workflow는 job이라는 단위의 step의 모음으로 구성되어 있다. 각 step은 쉘 스크립트를 수행하거나 action을 수행하며 하나의 runner(서버) 안에서 수행된다. 따라서 각 step은 순서대로 의존성을 갖게 된다. 또한 job끼리는 다른 runnder에서 수행되기 때문에 병렬로 실행이 가능하다.
- action은 반복되는 작업을 일컫는다.(예를 들어 코드를 pull한 뒤, build profile을 선택하여 build 등)
## How to apply Github Actions to Repository 
- repo에 ./github/workflows 폴더 생성
- 위 폴더 안에 workflow 정의 YAML 파일 생성
```
name: learn-github-actions
run-name: ${{ github.actor }} is learning GitHub Actions
on: [push]
jobs:
  check-bats-version:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '14'
      - run: npm install -g bats
      - run: bats -v
```
>[!note]
>- name: repo의 actions 탭에 표시되는 명칭
>- run-name: workflow 수행에 표시되는 명칭. 여기서는 `github` context 표현법을 사용함
>- on: workflow trigger로 여기서는 push 이벤트 발생시 workflow 수행 [참고 문서](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onpushpull_requestpull_request_targetpathspaths-ignore) 
>- jobs: workflow에 정의된 job 모음
>- check-bats-version: job 명칭
>- runs-on: job이 수행될 virtual machine
>- steps: check-bats-version job에서 수행할 action이나 shell script 모음
>- uses: 수행할 action을 설정
>- with: action 수행시에 사용할 파라미터(?). 여기서는 setup-node 수행시 node의 버전을 14로 설정
>- run: runner(서버)에서 수행할 command

![](Pasted%20image%2020221009105320.png)
- YAML파일 머지
- `on` 에 설정된 이벤트가 발생할 때 마다 action이 수행됨 (repo의 actions 탭에서 확인 가능)
## Links
- [Actions 마켓플레이스](https://github.com/marketplace?type=actions)