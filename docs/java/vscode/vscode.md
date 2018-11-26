# vscode

### vscode 引入 jq 代码提示

在项目目录创建 jsconfig.json,添加:

```
{
    "exclude": ["node_modules"],
    "typeAcquisition": {
      "include": [
        "jquery"
      ]
    }
  }
```

### 插件和设置同步

安装 Setting Sync 插件
