---
layout: post
title: "[JS] promise/async/await"

categories: [Log]

date: 2021-03-31
last_modified_at: 2021-03-31

toc: true
toc_sticky: true
---
JavaScript의 장점: 비동기
async&await: promise를 동기식처럼 작성할 수 있게 도와준다. (syntactic sugar)
promise만 사용하면 콜백 함수 지옥 -> async&await을 함께 사용하자...


**promise만 사용할 경우:**
<script src="https://gist.github.com/tempJ/6af42637408e0e809988d379664434a6.js"></script>


**async를 사용할 경우:**
<script src="https://gist.github.com/tempJ/790b22e57e11bed2c2b8a7079246d0b2.js"></script>
개발 시 훨씬 직관적이다.


**ex.**
<script src="https://gist.github.com/tempJ/49e3a2b133ee23a83d4a6be645c4e52b.js"></script>
이런 형태를


<script src="https://gist.github.com/tempJ/f9e7dee47335e79b8cec267d4d9240bc.js"></script>
이렇게... 기능은 같다.
단, 이렇게 작성한다면 user를 받아온 다음 data를 받아오기 때문에 동시에 실행 가능한 코드일 경우에는 시간이 오래 걸려서 바꿔줄 필요가 있다.
+await은 async가 붙은 함수 안에서만 사용할 수 있다.


**1**
<script src="https://gist.github.com/tempJ/27ccb6d2a110c8ef684e8bfcb2e8a7b3.js"></script>
promise를 각자 먼저 받아온 후, 뒤에서 한꺼번에 동기화 및 출력하는 방법. promise를 받아오면 곧장 promise에 해당하는 코드 블럭이 실행되기 때문에 userPromise와 dataPromise는 각각 병렬적으로 실행된다.


**2 Promise API 사용**
<script src="https://gist.github.com/tempJ/c5f630846899a048f3da7dc838be5226.js"></script>
Promise.all은 병렬적으로 promise를 다 받아올 동안 기다리는 API...


위와는 다른 실행결과지만,
<script src="https://gist.github.com/tempJ/84ef993b640d0493956091908d57fd2e.js"></script>
Promise.race API를 사용한다면 배열로 받아온 promise 중 가장 먼저 return하는 promise를 전달한다.