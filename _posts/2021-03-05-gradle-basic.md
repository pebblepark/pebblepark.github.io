---
title: Gradle 기초
tags:
  - gradle
---

`Gradle` 의 기초에 대해 알아보자.

### gradle 생성

```shell
$ gradle init
```

### project 폴더 구성

```
project/
    gradlew
    gradlew.bat
    gradle/wrapper/
        gradle-wrapper.jar
        gradle-wrapper.properties
    build.gradle
    settings.gradle
```

<br>

---

# Gradle의 3단계 빌드 스크립트 실행 순서
- Gradle은 3단계의 빌드 스크립트를 실행한다.
  1. 초기화
  2. 구성
  3. 실행

<br>

## 초기화 (설정로드)

Gradle의 기본 설정파일은 `settings.gradle` 로 루트 프로젝트 위치에 파일이 있어야 한다.

**- settings.gradle**
```
rootProject.name = 'gradle'
```

- `settings.gradle` 파일을 읽고 `Settings` 객체를 만든다
- 그후 `Settings` 객체를 이용하여 각 프로젝트의 `Project` 인스턴스를 만든다.
  
     
*[project interface](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html)*

> 이 인터페이스는 빌드 파일에서 Gradle과 상호 작용하는 데 사용하는 기본 API, Project모든 Gradle 기능에 프로그래밍 방식으로 액세스 할 수 있습니다


## 구성

`초기화`단계에서 생성된 `Project`객체 그에 연결된 `build.gradle`가 실행된다. `build.gradle`에 있는 `task`를 읽어다가 [`TaskContainer`](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.TaskContainer.html)를 이용해 `task`의 빈을 생성한 후 `Task 그래프`를 구성한다.

**- build.gradle**

```groovy
// build.gradle file
print "Hello\n"

task testTask {
    print "testTask running\n"

    doLast {
        print "Task do Last\n"
    }

    doFirst {
        print "Task do First\n"
    }
}

print "finish Configure\n"
```

**- 실행 결과**
```shell
$ gradle testTask

> Configure project :
Hello
testTask running
finish Configure

> Task :testTask
Task do First
Task do Last

BUILD SUCCESSFUL in 1s
1 actionable task: 1 executed
```


## 실행

`구성`에 있는 예제 실행까지 보여줬는데 `gradle [task]` 명령을 내렸을 때 [`TaskContainer`](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.TaskContainer.html)에서 `[task]`으로 입력한 테스크랑 같은 이름을 찾아서 실행시켜준다.


## 정리

- 초기화는 `setting.gradle`을 읽어서 `Project`인스턴스를 생성
- 구성은 `build.gradle`을 읽어서 `Project`을 실행
- 실행은 Consolec 명령으로 들어온 task를 찾아서 실행

<br>

---

# Task

- `gradle`의 실행 단위
- Console에 `gradle [task]` 명령을 통해 원하는 task를 실행할 수 있다.


- 기본 제공하는 task 몇가지 소개
  - gradle tasks : 내장 task 리스트를 보여준다.
  - gradle help –task (task명) : task명으로 작성한 task의 도움말 보여준다.
  - gradle build : 빌드한다.
  - gradle clean : 빌드 디렉토리를 삭제한다

<br>

### 기존 `testTask` 예제
`testTask running`은 **구성**단계에 출력되고 `Task do Last`와 `Task do First`는 **실행** 단계에서 실행이 되었다.

## Task의 생성
- **구성**단계에서 `testTask running`가 출력이 되는 이유
  - **구성**단계에서 `testTask`를 읽어 Bean 생성때 실행하기 때문이다.

## Task의 동작
**실행** 단계에서 Task의 동작 방식은 **일련의 [Action](https://docs.gradle.org/current/javadoc/org/gradle/api/Action.html)** 을 **순서대로** 실행해줄 뿐이다. 즉, Task는 일련의 Action객체로 구성되어 있다고 봐도 된다.

[Task 공식문서](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html#N18ED5)에 가면 더 다양한 Action을 볼 수 있다.


## Task 그래프
Task는 **다른 Task에 종속이 될 수 있다** 그리고 Task들간에 관계는 **[DAG(Directed acyclic graph)](https://ko.wikipedia.org/wiki/유향_비순환_그래프)** 로 작동한다

![그래프](https://yeh35.github.io/blog.github.io/img/documents/infra/gradle/task-dag-examples.png)


## Task 종속 테스크 지정

**- build.gradle**
```groovy
task taskX {
    dependsOn 'taskY'
    doFirst {
        println 'taskX doFirst'
    }

    doLast {
        println 'taskX doLast'
    }
}
task taskY {
    doLast {
        println 'taskY'
    }
}
```

**- 실행결과**
```shell
$ gradle taskX

> Task :taskY
taskY

> Task :taskX
taskX doFirst
taskX doLast
```



---

# 참고
- [초보자를 위한 Gradle 안내서](https://yeh35.github.io/blog.github.io/documents/infra/gradle/gradle-start1/)

## 나중에 좀 더 추가...
- [더 많은 task 예제](https://coding-start.tistory.com/305?category=820992)







