# 使用 Gitbook 打造电子书

### 使用 NPM 安装依赖和运行 gitbook 命令

- 全局安装 gitbook-cli: npm install gitbook-cli -g
- 安装依赖: npm install
- 根据目录文件[SUMMARY.md](docs/SUMMARY.md) 生成文件结构:npm run book-init
- 启动 web 服务预览\([http:\/\/localhost:4000](http://localhost:4000)\): npm run dev
- 打包成 html\(输出到\_book 文件夹\): npm run build
- 打包成 pdf\(需要安装 calibre:[https:\/\/calibre-ebook.com\/download_windows](https://calibre-ebook.com/download_windows)\): npm run pdf
- 发布\_book 到 Github Pages\(访问:[https:\/\/lujiandi.github.io\/book\/](https://lujiandi.github.io/book/)\) 或者 Gitee Pages\(访问:[https:\/\/lujiandi.gitee.io\/book\/](https://lujiandi.github.io/book/)\): npm run gh-pages

### 2019/1/9

> 要在代码片段里显示`{{message}}`,要写成`{% raw %}{{% endraw %}{ message }}`
