---
title: "JS 용어 정리: 연산자"
excerpt: "Java Script"

categories: ["Java Script"]
tags: ["연산자"]

date: 2023-06-07
last_modified_at: 2023-06-07

header:
  overlay_image: assets/images/Thumbnail/infinite.jpg
  overlay_filter: 0.5 
  teaser: assets/images/Thumbnail/js-thumb.png
published: true
---

# 연산자

---

JavaScript에서 연산자는 데이터를 조작하는 데 사용됩니다. 다음은 JavaScript에서 가장 일반적으로 사용되는 연산자의 종류입니다:

1. ## **산술 연산자**:

    산술 연산자는 수학적 연산을 수행합니다. `+`, `-`, `*`, `/`, `%` (나머지), `++` (증가), `--` (감소) 등이 있습니다.

```js
let a = 10;
let b = 20;

console.log(a + b); // 30
console.log(a - b); // -10
console.log(a * b); // 200
console.log(a / b); // 0.5
console.log(a % b); // 10
```

2. ## **할당 연산자**

    할당 연산자는 변수에 값을 할당합니다. `=`, `+=`, `-=`, `*=`, `/=`, `%=`, 등이 있습니다.

```js
let a = 10;
a += 5; // a = a + 5; a는 이제 15입니다.
```

3. ## **비교 연산자**: 

   비교 연산자는 두 값을 비교하고 논리적인 값을 반환합니다. `==` (동등), `===` (일치), `!=` (부등), `!==` (불일치), `<`, `>`, `<=`, `>=` 등이 있습니다.

```js
let a = 10;
let b = "10";

console.log(a == b); // true
console.log(a === b); // false
```

4. ## **논리 연산자**: 

   논리 연산자는 논리적인 연산을 수행합니다. `&&` (AND), `||` (OR), `!` (NOT) 등이 있습니다.

```js
let a = true;
let b = false;

console.log(a && b); // false
console.log(a || b); // true
console.log(!a); // false
```

5. ## **조건 (삼항) 연산자**: 

   조건 연산자는 세 개의 피연산자를 가지며, 조건에 따라 값을 선택합니다. `(조건) ? 값1 : 값2`

```js
let age = 15;
let status = (age >= 18) ? '성인' : '미성년자';

console.log(status); // "미성년자"
```

이러한 연산자들은 JavaScript에서 데이터를 조작하고, 조건을 평가하고, 값들을 비교하는 데 필수적인 도구입니다.

<br>

---

<br>