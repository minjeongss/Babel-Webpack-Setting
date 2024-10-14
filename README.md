# ES6+/ES.NEXT 최신 환경 설정

## babel

### package.json

```json
{
  "name": "es6-es.next-setting",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "babel src/js -w -d dist/js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "description": "",
  "devDependencies": {
    "@babel/cli": "^7.10.3",
    "@babel/core": "^7.10.3",
    "@babel/preset-env": "^7.25.8"
  }
}
```
