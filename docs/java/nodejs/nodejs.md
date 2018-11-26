#### win 下 npm 全局安装路径

1. 修改配置文件 npmrc(D:\developer\nodejs\node_modules\npm),添加:

   ```
   prefix=D:\developer\nodejs\node_global
   cache=D:\developer\nodejs\node_cache
   ```

2. 将 prefix 对应路径添加到 Path 环境变量
