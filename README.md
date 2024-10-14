# Babel, Webpack을 이용한 ES6+/ES.NEXT 개발 환경 구축 🎯

## 배경

ES6+/ES.NEXT 최신 ECMAScript 사양을 사용해 프로젝트를 진행하기 위해선,
구형 브라우저에서 문제 없이 동작시키기 위한 개발 환경 구축이 필요하다.

트랜스파일러인 Babel과 모듈 번들러인 Webpack을 이용해 개발 환경을 구축할 것이다.

### Babel

ES6+/ES.NEXT로 구현된 최신 사양의 소스코드를 구형 브라우저에서도 동작하는 `ES5 사양의 소스코드로 변환(트랜스파일링)` 할 수 있다.

Babel만으로 브라우저에서 트랜스파일링된 결과를 그대로 실행하면 에러가 발생한다.
브라우저는 CommonJS 방식의 require 함수를 지원하지 않기 때문이다. 이 문제를 해결하기 위해서 브라우저의 ES6 모듈(ESM)을 사용하도록 Babel을 설정할 수 있으나, ESM을 사용하는 것엔 문제가 존재한다.
해결법은 Webpack이다.

### Webpack

자바스크립트, CSS, 이미지 등 리소스들을 하나의 파일로 번들링하는 모듈 번들러다.
여러 개의 자바스크립트 파일을 하나로 번들링하기에, HTML 파일에서 script 태그로 여러 개의 자바스크립트 파일을 로드해야 하는 번거로움이 없다.

이때, ES5 사양의 소스코드로 트랜스파일링해도 브라우저가 지원하지 않는 코드가 남아 있을 수 있다.
이러한 기능을 구형 브라우저에서 사용하기 위해서 Polyfill을 사용할 수 있다.
Polyfill은 실제 운영 환경에서도 사용하기에 개발용 의존성으로 설치하지 않는다.

## 사용하는 패키지 및 플러그인

### Babel

```
npm install --save-dev @babel/core@7.10.3 @babel/cli@7.10.3
npm install --save-dev @babel/preset-env
```

### Webpack

```
npm install --save-dev webpack webpack-cli
npm install --save-dev babel-loader
npm install @babel/polyfill
```

## Babel 설정

### babelconfig.json

@babel/preset-env를 사용하겠다는 의미이다.

```json
{
  "presets": ["@babel/preset-env"]
}
```

### package.json

build를 진행했을 때, src/js에 있는 모든 자바스크립트 파일을 트랜스파일링하여 dist/js에 저장하겠다는 의미이다.

```json
{
  "scripts": {
    "build": "babel src/js -w -d dist/js"
  }
}
```

## Webpack 설정

### package.json

build를 진행했을 때, Babel 대신 Webpack을 실행하도록 한다.

```json
"scripts": {
    "build": "webpack -w"
  },
```

### webpack.config.js

npm run build를 실행했을 때, dist/js/bundle.js가 생기며 lib.js와 main.js가 아닌 하나로 번들링된 bundle.js로 index.html에서 실행할 수 있다.

```js
const path = require("path");
module.exports = {
  entry: ["@babel/polyfill", "./src/js/main.js"],
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "js/bundle.js",
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        include: [path.resolve(__dirname, "src/js")],
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env"],
          },
        },
      },
    ],
  },
  devtool: "source-map",
  mode: "development",
};
```

## Polyfill 설정

### main.js

가장 첫 번째 줄에 Polyfill을 로드한다. 이는 Webpack을 사용하지 않는 환경에서 실행하는 설정이다.

```js
import "@babel/polyfill";
```

### webpack.config.js

Webpack을 사용하는 환경이라면 entry에 Polyfill을 추가한다.

```js
const path = require("path");
module.exports = {
  entry: ["@babel/polyfill", "./src/js/main.js"],
};
```
