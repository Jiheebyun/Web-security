
#  Web을 위한 블로그 입니다.

## React:  

<details>
  <summary>useState</summary>
  
- useState 어떤 구조가지고 있는지 고민해보자

[참조]: 모던리액트 Deep Dive
```jsx
function useState*(initialValue){
  let internalState = initialValue

  function setState(newValue){
    internalState = newValue
  }

  return [ internalState, setState ]
}
```


- 이 코드에서 `setValue`를 호출해도 `value` 값이 변경되지 않는 이유는 `useState`가 React 컴포넌트의 상태를 관리하는 방식 때문이다.

```jsx
const [value, setValue] = useState(0);
setValue(1);
console.log(value); // 0
```

1. **비동기 상태 업데이트**: React의 상태 업데이트 함수(`setValue`)는 비동기적으로 작동한다. 즉, 상태를 업데이트하라는 요청을 보낸 후 바로 상태가 변경되지 않고, React가 다음 렌더링 사이클에서 상태를 업데이트한다. 그래서 `setValue`를 호출한 직후에는 상태가 즉시 변경되지 않은 상태로 유지된다.

2. **구조 분해 할당**: 코드에서 `const [value, setValue] = useState(0);`는 현재 상태 값과 상태 업데이트 함수를 반환한다. 이때 반환된 `value`는 초기 값(0)을 가지게 된다. 이후 `setValue(1)`을 호출해도, 이 시점에서는 아직 상태 업데이트가 반영되지 않았기 때문에 `value`는 여전히 0이다.

3. **동기식 로그 호출**: `console.log(value)`는 `setValue` 호출 직후에 실행되므로, 상태 업데이트가 완료되기 전에 `value`를 출력하게 된다. 따라서 여전히 초기 값인 0을 출력하게 되는 것이다.

이를 해결하기 위해서는 상태 업데이트 후 다시 렌더링된 컴포넌트에서 새로운 상태 값을 확인해야 한다. React는 상태가 업데이트되면 자동으로 컴포넌트를 다시 렌더링하여 최신 상태 값을 반영한다.
  
</details>







## CS:

<details>
  <summary>Xml Json Yaml 데이터</summary>

## Xml json Yaml 데이터


### XML (eXtensible Markup Language)
XML은 데이터를 구조화하기 위한 마크업 언어이다. 태그를 사용하여 데이터를 계층적으로 표현한다. 주로 문서 저장 및 전송에 사용된다.

**예시:**
```xml
<book>
    <title>Learning XML</title>
    <author>John Doe</author>
    <year>2021</year>
</book>
```
위 예시에서는 `<book>` 태그 안에 책의 제목, 저자, 출판연도를 태그로 감싸서 구조화하고 있다.

### JSON (JavaScript Object Notation)
JSON은 데이터를 저장하고 전송하기 위한 경량 데이터 교환 형식이다. 자바스크립트 객체 표기법을 사용하여 데이터를 표현한다. 주로 웹 애플리케이션에서 데이터 교환에 사용된다.

**예시:**
```json
{
    "title": "Learning JSON",
    "author": "Jane Doe",
    "year": 2022
}
```
위 예시에서는 JSON 객체 안에 책의 제목, 저자, 출판연도를 키-값 쌍으로 표현하고 있다.

### YAML (YAML Ain't Markup Language)
YAML은 사람이 읽기 쉬운 데이터 직렬화 형식이다. 들여쓰기를 사용하여 데이터를 계층적으로 표현한다. 주로 설정 파일에 사용된다.

**예시:**
```yaml
title: Learning YAML
author: Alice Doe
year: 2023
```
위 예시에서는 YAML 형식으로 책의 제목, 저자, 출판연도를 들여쓰기를 통해 계층적으로 표현하고 있다.

### 요약
- XML은 데이터를 태그로 감싸서 구조화한다.
- JSON은 데이터를 키-값 쌍으로 표현하며 주로 웹에서 사용된다.
- YAML은 들여쓰기를 통해 데이터를 구조화하며 사람이 읽기 쉽다.

이들 포맷은 각각의 장점과 사용 사례가 다르므로 상황에 맞게 선택해서 사용하면 된다.

</details>



## Web:
<details>
  <summary>rss서비스가 제공하는 Xml 파싱</summary>
  ## Xml json Yaml 데이터

  
아래는 fetch를 사용하여 RSS 피드를 가져오고, TextDecoder를 사용하여 인코딩 


```jsx

// RSSItem 생성 함수
const createRSSItem = (title, link, description, pubDate) => ({
    title,
    link,
    description,
    pubDate,
    toString() {
        return `RSSItem(title=${this.title}, link=${this.link}, description=${this.description}, pubDate=${this.pubDate})`;
    }
});

// XML 데이터를 파싱하여 RSSItem 배열로 변환하는 함수
const parseRSS = (xml) => {
    const parser = new DOMParser();
    const xmlDoc = parser.parseFromString(xml, "application/xml");
    const items = Array.from(xmlDoc.getElementsByTagName("item"));

    return items.map(item => createRSSItem(
        item.getElementsByTagName("title")[0].textContent,
        item.getElementsByTagName("link")[0].textContent,
        item.getElementsByTagName("description")[0].textContent,
        item.getElementsByTagName("pubDate")[0].textContent
    ));
};

// URL에서 RSS 피드를 가져오고 파싱된 RSSItem 배열을 반환하는 함수
const fetchRSSFeed = async (url) => {
    try {
        const response = await fetch(url);
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }

        const buffer = await response.arrayBuffer();
        const decoder = new TextDecoder('utf-8');
        const xml = decoder.decode(buffer);

        return parseRSS(xml);
    } catch (error) {
        console.error("Failed to fetch RSS feed:", error);
        return [];
    }
};

// 사용 예제
const rssUrl = 'https://example.com/rss';
fetchRSSFeed(rssUrl).then(rssItems => {
    rssItems.forEach(item => console.log(item.toString()));
});


```
</details>


<details>
  <summary>iframe이란 </summary>
## iframe

iframe은 한 웹 페이지 안에 다른 웹 페이지를 포함시킬 수 있는 HTML 요소이다. 이를 통해 외부 콘텐츠를 현재 페이지에 표시할 수 있으며, 주로 다음과 같은 목적으로 사용된다
즉, 하나의 웹 페이지 내부에 다른 웹 페이지를 불러와서 보여줄 수 있는 기능을 제공한다는 의미이다.


현재 페이지 (main.html)
```jsx
<!DOCTYPE html>
<html>
<head>
    <title>Main Page</title>
</head>
<body>
    <h1>이것은 메인 페이지입니다.</h1>
    <iframe src="iframe-content.html" width="600" height="400"></iframe>
</body>
</html>

```

포함된 페이지 (iframe-content.html)

```jsx
<!DOCTYPE html>
<html>
<head>
    <title>Iframe Content</title>
</head>
<body>
    <h1>이것은 iframe 내부의 페이지입니다.</h1>
</body>
</html>
```
설명
- main.html 파일에는 iframe 태그가 있으며, src 속성을 통해 iframe-content.html 파일을 불러온다.
- iframe-content.html 파일은 독립된 HTML 문서로, 자신의 <html>, <head>, <body> 태그를 가진다.
- 브라우저는 main.html을 렌더링할 때, iframe을 만나면 iframe-content.html을 별도로 로드하여 iframe 영역에 표시한다.
- iframe은 현재 페이지 내에서 별도의 브라우저 창처럼 동작한다.
- iframe 태그 안에 불러온 문서는 현재 페이지의 일부로 보이지만, 사실은 독립적인 HTML 문서이다.


이와 같이 iframe을 사용하면 하나의 HTML 문서 내에 또 다른 HTML 문서를 포함시키는 효과를 얻을 수 있지만, 이는 단순히 두 개의 HTML 문서를 포함하는 것이 아니라, 현재 페이지 내에 별도의 브라우저 창을 생성하여 다른 HTML 문서를 로드하는 것이다. 각 문서는 독립적으로 존재하며, 각자의 <html>, <head>, <body> 태그를 가진다.

그럼 iframe을 왜사용하는 걸까? 

iframe을 사용하는 이유는 다양한 외부 콘텐츠를 현재 웹 페이지에 간편하게 포함시키고, 이러한 콘텐츠를 독립적으로 관리할 수 있기 때문이다.

iframe의 사용 목적<br/>
1. 외부 콘텐츠 포함
- 다른 웹사이트나 서비스에서 제공하는 콘텐츠를 현재 웹 페이지에 포함시킬 때 유용하다.
- 예: 유튜브 비디오, 구글 맵, 외부 광고 배너 등.
2. 독립적인 콘텐츠 관리
- iframe으로 포함된 콘텐츠는 현재 페이지와 독립적으로 작동하므로, 스타일이나 스크립트 충돌을 피할 수 있다.
- 예: 다양한 소스에서 가져온 데이터를 표시할 때 각 소스의 스타일이나 스크립트가 충돌하지 않도록 하기 위해 사용.
3. 보안과 격리
- iframe은 포함된 콘텐츠를 현재 페이지와 격리시키므로 보안상의 이유로도 사용된다.
- 예: 외부 콘텐츠가 현재 페이지에 영향을 미치지 않도록 하기 위해 사용.

</details>





## Docker:  












<details>
  <summary>Docker hub: push</summary>

### 도커 허브 로그인
- 먼저, 로컬 컴퓨터에서 도커 허브에 로그인한다. docker login 명령어를 터미널에서 실행하고, 도커 허브 계정의 사용자 이름과 비밀번호를 입력한다. 이 과정을 통해 도커 허브에 인증된다.
### 이미지 생성 또는 태그 추가
- 도커 이미지를 로컬에서 생성하거나 이미 생성된 이미지에 태그를 추가한다. 이미지가 이미 있는 경우 docker tag 명령어를 사용하여 해당 이미지에 도커 허브로 푸시할 때 사용할 태그를 추가한다. 예를 들어, docker tag my-image username/my-image:tag와 같이 한다.
### 도커 이미지 푸시
- 도커 이미지를 도커 허브에 푸시한다. docker push username/my-image:tag 명령어를 사용하여 로컬에 있는 이미지를 도커 허브에 업로드한다. 이 과정은 이미지의 크기에 따라 시간이 걸릴 수 있다.
### 푸시 확인
- 푸시가 완료된 후, 도커 허브에 로그인하여 업로드된 이미지를 확인한다. 도커 허브 웹사이트에서 저장소(repository)를 열어 푸시된 이미지가 제대로 올라갔는지 확인할 수 있다.

</details>

<details>
  <summary>form</summary>
</details>

1. docker 부분옮기기
2. 오저버패턴 적용해서 사용하기





