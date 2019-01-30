# nodejs

> 采用 CommonJS 模块化规范

#### win 下 npm 全局安装路径

1. 修改配置文件 npmrc(D:\developer\nodejs\node_modules\npm),添加:

   ```
   prefix=D:\developer\nodejs\node_global
   cache=D:\developer\nodejs\node_cache
   ```

2. 将 prefix 对应路径添加到系统 Path 环境变量

#### 运行.js 文件

> 命令行运行：node xxx.js

#### npm 命令

> npm 是 nodejs 包管理工具

- npm init

  > 生成 package.json

- npm run 自定义命令

  > 运行 package.json(scripts)

- npm install cnpm -g --registry=http://registry.- npm.taobao.org

- npm install xxx@xxx -g

  > 全局安装

- npm install xxx@xxx -save

- npm install xxx@xxx --save-dev
