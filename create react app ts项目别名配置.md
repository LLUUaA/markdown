# React typescript下的 路径别名配置

## 一、通过create-react-app 搭建react项目

react 如果使用了脚手架来创建项目比如
**npx create-react-app my-app --template typescript**
创建了一个 typescript的 react项目 ，如果想在项目中引入组件等等
默认情况下这样很难受:

```javascript
import Component from '../components/A'
```

如果想要这样的

```javascript
import Component from '@/components/A'
```

该如何配置呢？下面例子将配置antd按需加载和项目别名配置

### 二、设置别名alias	

1.首先 **npm install babel-plugin-import customize-cra react-app-rewired --save-dev**

2.在项目根目录新建 **config-overrides.js**

注：**babel-plugin-import**为按需加载**antd**所需，如果不需要可按实际情况安装

```javascript
const { override, fixBabelImports,addWebpackAlias,addDecoratorsLegacy } = require('customize-cra');
const path = require('path')

module.exports = override(
    fixBabelImports('import', {
        libraryName: 'antd',
        libraryDirectory: 'es',
        style: true,
    }),
    addWebpackAlias({
        ['@']: path.resolve(__dirname, './src') // 配置别名
    }),
    addDecoratorsLegacy(), // es7装饰器
);
```

3.修改**package.json**

```javascript
  "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test",
    "eject": "react-scripts eject"
  },
```

4.在tsconfig.json**文件中新增paths

```javascript
{
  "compilerOptions": {
    "experimentalDecorators": true, // es7装饰器
    "baseUrl": "src",
    "paths": {
      "@/*": ["src/*"] // ts编译路径
    },
    ...
  }
}
```

第一步没什么问题，然而在于第二个步骤，当你npm run start启动项目的时候你会发现tsconfig.json文件中的paths不见了！

解决方法为：在根目录新建一个文件命名为**paths.json**，同**tsconfig.json**配置

```	javascript
{
  "compilerOptions": {
    "baseUrl": "src",
    "paths": {
      "@/*": ["src/*"]
    },
    ...
  }
}
```

然后在tsconfig.json中新加：

```bash
"extends": "./paths.json"
```

然后项目中就能正常使用@别名了。