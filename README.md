# Mock Server
借助强大的 [Swagger](https://swagger.io/) 生成服务器端模拟数据。

## 启动
1. 安装依赖: `npm i`
1. 安装 swagger: `npm install -g swagger`
1. `cd src`
1. 启动 `npm start`
1. 编辑或预览 `npm run edit`

## 其他
接口数据存在： `src/api/swagger/swagger.yaml`。

## 制作步骤
1 制作 Model 和 ModelList。

```
Model名称:
    properties:
      id: 
        type: string
        x-mock: id
     
Model名称List:
    properties:
      list:
        type: array
        items:
          $ref: '#/definitions/Model名称'
```
放在 definitions 下。

注意，如果要生成特定格式的

2 完善 Model。
```
Model名称:
    properties:
      id: 
        type: string
      xxx:
        type: string
```

3 生成器生成 tag 和 path。

4 将生成的内容分别放到 tags 和 paths 下。

## mock 模式返回数据优化
改法见[这里](src/should-change-file)。具体见这个[use mock.js to solve static mock data](https://github.com/swagger-api/swagger-node/issues/384)。

需要更多定制化数据，可以用在 model 上设置 x-mock 设置值，并在 `getMockValue` 中处理。目前支持 x-mock: id，生成随机的 guid。

注：本来准备用所有接口用同一个 `operationId`，通过 `/swagger` 获得当前接口的 Model，结合 Mockjs 来生成随机数据的，但发现框架要求每个 `operationId` 的值是不能重复的，坑。。。
