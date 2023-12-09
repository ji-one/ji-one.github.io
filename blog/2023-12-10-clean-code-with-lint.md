---
slug: clean-code-with-lint
title: Lint 도입기 (feat. Spotless)
tags: [clean code, spotless]
---

### 왜 Lint를 사용해야 할까?

나는 코딩 스타일을 중요하게 생각한다. 문단이 적절하게 나누어져 있고 띄어쓰기가 정확한 글이 읽기 좋은 것처럼, 코드도 가독성이 좋아야 한다.

특히 여러 명이 참여하는 프로젝트라면 모든 팀원이 동일한 코딩 스타일을 따르는 게 더욱 중요하다. 일관된 코드 스타일을 유지해야 서로의 코드를 빠르게 이해할 수 있고 유지보수가 쉬워져 결론적으로 소프트웨어의 품질을 향상시킬 수 있기 때문이다.

현재 내가 속한 팀은 [Lint](<https://en.wikipedia.org/wiki/Lint_(software)>)를 적용하고 있지 않아서, 코드 포맷팅 관련 리뷰에 많은 리소스를 사용하고 있다. 이러한 리소스를 비즈니스 로직에 집중하기 위해 Lint를 도입하기로 했다.

### 그렇다면 어떤 Lint를?

[spotless](https://github.com/diffplug/spotless/tree/main)를 선택했다.

spotless에서 지원하는 명령어 중 `spotlessApply`를 사용하면 코드가 컨벤션에 맞게 자동으로 수정된다. 따라서 효율적으로 코딩 스타일을 준수할 수 있을 것으로 판단했다. 또한, `spotlessCheck`로 코드가 정해진 컨벤션을 지키고 있는지 검사할 수도 있다.

### Lint 검사를 자동화하자

가장 먼저, `spotlessCheck`에 실패하면 CI가 실패하도록 했다.

그러나 CI가 실패하고 나서야 린트 오류임을 인지하는 게 불편할 것 같아서 [husky](https://typicode.github.io/husky/)를 사용해 `spotlessCheck`에 성공하지 못하면 푸시도 실패하도록 설정했다.
커밋 전에 검사하지 않는 이유는, 커밋을 잘게 나누어 자주 하는 팀원들에게는 오히려 번거로울 수 있다고 생각했다.

푸시에 실패하면 자동으로 `spotlessApply`를 실행하는 것도 고려했지만(심지어 커밋까지 자동으로 되도록), 개발자가 인지하지 못한 상태에서 변경 사항이 생기는 것은 위험하다고 판단되어 적용하지 않았다.

### 남은 과제

현재 설정은 아래와 같이 기본적인 규칙만 적용했다. 앞으로 팀원들과 함께 리뷰에서 어떤 코드 규칙을 추가할지 논의하고자 한다.

```
spotless {
    java {
        importOrder() // import 문 정렬
        removeUnusedImports() // 사용되지 않는 import 문 제거
        trimTrailingWhitespace() // 행 끝의 공백 제거
        endWithNewline() // 파일 끝에 개행 문자 추가
    }
}
```
