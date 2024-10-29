# 1. Progressive rendering이란 무엇인가요?

- 정의: CSR과 SSR의 장점을 결합한 기술로, Server에서 중요한 콘텐츠를 렌더링한 후, 중요하지 않은 콘텐츠를 기다리지 않고 Client로 Streaming하고, 이후에 Server에서 나머지 중요하지 않은 콘텐츠들이 렌더링되면 다시 Client의 웹페이지로 Streaming하는 기술이다.

```
동작방식
    1. Client에서 Server로 HTML을 요청
    2. Server에서 API Request 생성 후, 중요한 콘텐츠를 우선 렌더링 후 Client로 Streaming
    3. Client는 Server에서 내려준 HTML을 chunk 로드 후 렌더링.
    4. Server에서 중요하지 않았던 나머지 콘텐츠를 렌더링하고 다시 Client로 Streaming.
    5. Client는 Server에서 중요치 않았던 나머지 콘텐츠를 받아 렌더링.
    6. 전체 페이지 로드 완료 후, Client는 일반적으로 이벤트 핸들러 및 기타 상호작용 동작을 연결하는 DOM Element에 대한 상호작용을 수행.
```

- 장점

  - Server에서 중요한 콘텐츠를 먼저 Client로 Streaming할 수 있다
  - Javascript bundle을 로드하는 시간동안 콘텐츠 렌더링을 차단하지 않는다.
  - CSR과 SSR의 장점을 모두 가지고 있어 페이지 로드시간이 빠르다.

- 단점
  - 콘텐츠가 빠르게 표시 되더라도 마지막 중요하지 않은 콘텐츠가 모두 로드된 후, Javascript 관련 상호 작용이 활성화 된다.
  - PSSR이 확립된 프레임워크가 아직 없으며, 웹 어플리케이션에 한계에 대한 의존도가 높다.

# 2. 페이지 로드 시간을 줄이는 세 가지 방법에 관해서 이야기 해 보세요

- 이미지 최적화

  - 이미지 크기 조정 : 필요 이상으로 큰 이미지를 사용하지 않고, 화면에 적합한 크기로 조정하여 로드 시간을 줄인다.
  - 최적 포맷 사용 : JPEG, PNG, SVG, WebpP 등 가장 적합한 포맷을 선택하여 용량을 줄인다. Webp는 고압축 포맷으로 이미지 품질을 유지하면서 용량을 줄일 수 있다.
  - 지연 로딩(Lazy Loading) : 스크롤하면서 필요한 시점에 이미지를 로드하도록 설정하여 초기 로딩 속도를 개선한다.
    ```js
    <img src="image.jpg" alt="Lazy Loaded Image" loading="lazy">
    ```

- javascript 및 css 최적화

  - 코드 압축(Minification) : JavaScript와 CSS 파일을 압축하여 파일 크기를 줄인다. 이를 통해 네트워크 전송 속도를 높일 수 있다.
  - 불필요한 JavaScript 지연 로딩 : async나 defer 속성을 사용해 JavaScript 파일 로딩이 초기 렌더링을 방해하지 않도록 한다다.
  - 필요한 코드만 로드 : 코드 스플리팅(Code Splitting)을 통해 페이지별로 필요한 JavaScript와 CSS만 로드한다.
  - 크리티컬 CSS 사용 : 필수적인 스타일만 인라인으로 작성해 첫 화면을 빠르게 렌더링하고, 나머지 CSS는 비동기 로드한다.

- 캐싱
  - 브라우저 캐싱 설정 : CSS, JavaScript, 이미지 같은 정적 리소스는 캐싱 설정을 통해 재방문 시 로드 시간을 줄일 수 있다.
  ```js
  Cache-Control: public, max-age=31536000
  ```
  - 서비스 워커 : 오프라인 및 백그라운드에서 리소스를 캐싱해 페이지 로딩 성능을 인다.
