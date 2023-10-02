---
tags: dev, gradle
---
## Getting Start
### init gradle project
![](Pasted%20image%2020221118230605.png)
### gradle project structure
![](Pasted%20image%2020221118230657.png)
## gradle 핵심 concept
- Build Configuration
- Tasks & Plugins
- Dependency Management
- Build Lifecycle
## Build Configuration
Configuration은 Settings와 Projects로 구성되어 있다
- Settings는 settings.gradle에 정의된다
	- Root Project 명
	- Sub Project 목록
- Sub Project는 build.gradle에 정의된다
	- Plugins
	- Dependencies
	- Tasks
	- Source Code
![](Pasted%20image%2020221118225532.png)
## Build Lifecycle
- Initialization: settings.gralde을 읽어 대상 Project들을 확인한다 
- Configuration: 각 Projects에 있는 build.gradle을 실행하고, Project 및 Task Object Model을 Build한다
- Execution: task를 수행한다
## Plugins
gradle의 기능을 확장하기 위해 configuration에 적용할 수 있는 재사용 가능한 공통 기능이다.
### Plugin의 기능
- task를 추가한다
- configuration을 초기화한다
- 새로운 configuration model을 추가한다
### Plugin Type
- Built-in: Gradle에 포함된 plugin [Gradle Plugin Reference](https://docs.gradle.org/current/userguide/plugin_reference.html#header)
- Community: plugin repository에서 download한 plugin으로 version명시 필요
- Local: 로컬에서 구현한 plugin [Gradle - Plugins](https://plugins.gradle.org/search?term=junit)
![](Pasted%20image%2020221118234010.png)
### Plugin 예시) Java
-  source code 위치를 위한 configuration 설정: SourceSets
	- src/main/java
	- src/test/java
	- 아래와 같은 방식으로 소스 위치를 변경할 수도 있다
	  ![](Pasted%20image%2020221118235553.png)
- compileJava, test 같은 Task 추가
## Tasks
### Task Concept
- compile, test, document생성 등 gradle 작업의 기본 단위이다.
- 각 project마다 각자의 task를 가지고 있을 수 있다.
- task 간에 선, 후행 관계가 있을 수 있다.
- 종류
	- Built-in: init, wrapper
	- Plugin 제공: compile, test
	- Custom
- input(파일 혹은 설정) -> action(수행) -> output(결과물) 단계로 이루어진다.
- 예시
	- configuration properties를 통한 input (compile option, test report 생성 directory)
	  ![](Pasted%20image%2020221121093746.png)
	- test task의 선, 후행 관계
	  ![](Pasted%20image%2020221121093639.png)
### Task Logging Option
- `--console=plain` or `--console=verbose`: 실행된 종속 task를 보여줄지 결정한다
- `-q`: 로그 메시지를 숨기고 output만 표시된다
- `--dry-run`: 실제 실행 없이 어떤 일이 발생할지만 보여준다
- gradle.properties에 아래와 같은 방식으로 설정하는 것도 가능하다
  ![](Pasted%20image%2020221121095816.png)
### Task 수행결과 Label
- No Label(or EXECUTED): 수행된 태스크
- UP-TO-DATE: 기존에 수행된 태스크로 대체
- NO-SOURCE: input과 output이 있지만 source가 없는 태스크(`JavaCompile` task의 source는 .java 파일이다)
- FROM-CACHE: 이전 실행 결과에서 task의 output을 찾을 수 있는 경우
### Custom Task
- task 생성은 configuration이 read되는 걸 말한다. 
- `register`와 `named`
	- tasks의 property인 `tasks.register("name")`를 사용해 task를 생성하지 않고 task를 정의할 수 있다. 
	- `tasks.named("name")`를 사용하면 task를 생성하지 않고 task configuration을 정의할 수 있다. (plugin에서 제공하는 경우, Kotlin DSL을 사용하여 `tasks.name` 형식으로도 작성 가능)
- tasks.create나 기타 eager tasks는 사용을 피해야 한다[Task Configuration Avoidance](https://docs.gradle.org/current/userguide/task_configuration_avoidance.html#task_configuration_avoidance)
  ![](Pasted%20image%2020221121235706.png)
- task명은 모든 project를 통틀어 유니크해야 한다.
	- task 키워드를 사용한 생성
		- task myTask
		- task myTask { configure closure}
		- task myTask(type: SomeType)
		- task myTask(type: SomeType){ configure closure }
  ![](Pasted%20image%2020221121213332.png)
  - `dependsOn`으로 task간 선후행 관계를 정할 수 있다. I/O를 사용한 종속 관계 설정도 가능하다. [Linking outputs to inputs](https://docs.gradle.org/current/userguide/more_about_tasks.html#sec:link_output_dir_to_input_files)
  ![](Pasted%20image%2020221121214424.png)
  - `group` property를 이용해 grouping 하는 것이 가능하다. (IDE에 같은 그룹 task로 표기)
  - description property로 ./gradew tasks --all로 task목록을 출력했을 때 나오는 설명을 지정할 수 있다.
	  - 예) Distribution task group에 zipTestResult라는 task를 추가하고, "Archive...."라는 설명을 추가했다. 
    ![](Pasted%20image%2020221121214540.png)
  ![](Pasted%20image%2020221121214549.png)
  - task에 parameter를 넘기는 방법
	  - 넘길 때: ./gradlew zipTestResult - PzipName=test.zip
	  - 받을 때: providers.gradleProperty()
	    ![](Pasted%20image%2020221121214724.png)
- doFirst & doLast
## Dependency
- `repositories`를 통해 외부에서 다운로드할 수 있다
  ![](Pasted%20image%2020221122093318.png)
  - module은 유니크한 group과 library name으로 정의되며, 버전을 명시할 수 있다.  
    ![](Pasted%20image%2020221122093405.png)
- configuration을 통해 종속성의 scope을 정하여 task가 필요할 때 접근할 수 있게 할 수 있다. (test를 complie하기 위해, source를 compile하기 위해, runtime에 필요할 때) 
	- [configuration scope 비교](https://docs.gradle.org/current/userguide/java_library_plugin.html#sec:java_library_recognizing_dependencies)
	- `api` public facing specification (public interface에서 사용되는 class 등)
		- 멀티 모듈일 경우, 다른 모듈에서 해당 라이브러리 참조 가능
	- `implementation` intenally used (계산 라이브러리)
		- 멀티 모듈일 경우, 다른 모듈에서 해당 라이브러리 참조 불가
	- `runtimeOnly` application 실행에 필요 (특정 로깅 라이브러리)
	- `testImplementation` test시 필요
- module이 자신의 종속성을 가지고 있는 경우에 gradle은 자동으로 가져온다
	- subproject가 있는 경우 `./gradlew <subproject name>:dependencies [--configuration <configuration name>]`로 모든 종속성을 조회할 수 있다
	- `./gradlew <subproject name>:dependencyInsight --dependency <dependency name>` 으로 상세한 정보를 얻을 수 있다
	  ![](Pasted%20image%2020221122223847.png)
- module version을 명시하는 방법
  ![](Pasted%20image%2020221122095212.png)
	- Dynamic version: version이 변경될 수 있다 (e.g. `2.+` 은 2.6이었다가 다음 날 2.7이 될 수도 있다)
	- Changing version:  향후 업데이트 될 version과 동일한 artifact (주로 다음버전의 기능을 담고 있는 SNAPSHOT)
	- gradle은 default로 Dynamic & Changing version에 대해 24시간 caching을 적용한다.
	  ![](Pasted%20image%2020221122214703.png)
	  - 직접적 혹은 간접적으로 다수의 dependency가 요구되는 경우 strict version이 정의되어있지 않는 한, 가장 높은 버전이 선택된다
	    ![](Pasted%20image%2020221122214924.png)
## Publishing
- 특정 repository에 소프트웨어 artifacts를 publish 할 수 있다. (Java POM file같은 관련 metadata는 자동 생성된다.)
  ![](Pasted%20image%2020221123094030.png)
## Multi-Project Build
  - multi project로 이루어진 application의 경우, gradle에서는 이를 subproject로 함께 관리할 수 있다.
### Multi-Project Structure
  - settings.gradle에 각 subproject를 등록한다
    ![](Pasted%20image%2020221123095240.png)
- 각 subproject에 build.gradle을 만든다
  ![](Pasted%20image%2020221123095333.png)
  - subproject는 다른 subproject를 종속성으로 가지고 있을 수 있다. 
    `implementation(project(":other-pjt"))` or `implementation(projects.other-pjt)`
    ![](Pasted%20image%2020221123095419.png)
### Sharing Common Configuration
- 공통 configuration을 한 군데서 정의하고 참조할 수 있다.
- 아래 예시에서 buildSrc는 특수한 subproject이며(settings.gradle에 정의하지 않음), app과 model에서 buildSrc의 configuration을 가져오고 있다. (통상 custom task에 사용할 때는 참조되는 쪽에서 custom task를 생성하고, 참조하는 쪽에서는 configuration만 작성하는 것이 좋다. 이렇게 함으로써 각 build.gradle에서는 같은 task에 대해 각자의 configuration을 만들 수 있다.)
- [Sharing build logic between subprojects Sample (gradle.org)](https://docs.gradle.org/current/samples/sample_convention_plugins.html)
![](Pasted%20image%2020221123123658.png)
![](Pasted%20image%2020221123123449.png)
![](Pasted%20image%2020221123123459.png)

![[Pasted image 20230129235706.png]]