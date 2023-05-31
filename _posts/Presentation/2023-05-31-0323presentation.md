---
title: "23-03-23 Presentation"
excerpt: "REST API"

categories: ["Presentation"]
tags: ["REST API"]

date: 2023-03-23
last_modified_at: 2023-05-31
---

# Q1. 다음 기능에 대한 REST API 모범 사례를 연구해서 제출하세요
---

1. 특정 블로그 게시물의 댓글 검색
   - URI 경로의 맨 끝에 댓글 ID 또는 댓글과 연결된 블로그 게시물 ID를 사용하는 것이 좋습니다. (e.g., /posts/{postId}/comments/{commentId}) [**[1](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)**]
   - GET 요청 메서드를 사용하여 댓글을 검색합니다. [**[1](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)**]
2. 블로그 게시물 좋아요
   - 좋아요를 추가하려면 POST 요청 메서드를 사용하고, 좋아요를 취소하려면 DELETE 요청 메서드를 사용하는 것이 좋습니다. [**[4](https://www.freecodecamp.org/news/rest-api-best-practices-rest-endpoint-design-examples/)**]
   - 좋아요를 나타내는 자원은 URI 경로의 게시물 ID 뒤에 /likes를 사용하여 생성할 수 있습니다. [**[1](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)**]
3. 블로그 게시물 좋아요 취소
   - 좋아요를 취소하려면 DELETE 요청 메서드를 사용하는 것이 좋습니다. [**[4](https://www.freecodecamp.org/news/rest-api-best-practices-rest-endpoint-design-examples/)**]
4. 다른 작성자 팔로우
   - 팔로우를 나타내는 자원은 URI 경로의 작성자 ID 뒤에 /followers를 사용하여 생성할 수 있습니다. (e.g., /authors/{authorId}/followers) [**[7](https://www.bacancytechnology.com/blog/rest-api-best-practices)**]
   - 팔로우를 추가하려면 POST 요청 메서드를 사용하는 것이 좋습니다. [**[7](https://www.bacancytechnology.com/blog/rest-api-best-practices)**]
5. 댓글 작성
   - 새 댓글을 작성하려면 POST 요청 메서드를 사용하고, 댓글을 수정하려면 PUT 요청 메서드를 사용하는 것이 좋습니다. [**[4](https://www.freecodecamp.org/news/rest-api-best-practices-rest-endpoint-design-examples/)**]
   - 댓글을 나타내는 자원은 URI 경로의 게시물 ID 뒤에 /comments를 사용하여 생성할 수 있습니다. (e.g., /posts/{postId}/comments) [**[1](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)**]



---
