# 使用 Gitbook 打造电子书

### 使用 NPM 安装依赖和运行 gitbook 命令

* 全局安装 gitbook-cli: npm intall gitbook-cli -g
* 安装依赖: npm install
* 根据目录文件[SUMMARY.md](docs/SUMMARY.md) 生成文件结构:npm run book-init
* 启动 web 服务预览\([http:\/\/localhost:4000](http://localhost:4000)\): npm run dev
* 打包成 html\(输出到\_book 文件夹\): npm run build
* 打包成 pdf\(需要安装 calibre:[https:\/\/calibre-ebook.com\/download\_windows](https://calibre-ebook.com/download_windows)\): npm run pdf
* 发布\_book 到 Github Pages\(访问:[https:\/\/lujiandi.github.io\/gitbook-md\/](https://lujiandi.github.io/gitbook-md/)\): npm run gh-pages

