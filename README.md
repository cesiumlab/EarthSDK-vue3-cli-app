# earthsdk-vue-cli-app

## 简介

通过vue-cli4创建项目，然后再基于vue3.x和EarthSDK创建地球。  
如果是大型项目，可以参考这种方式来构建程序。  
本项目，相当于在vue-cli的基础上做一些稍许修改，就可以加载EarthSDK了。  

注意：该项目可以配置成使用纯Cesium开发，看说明文档最下方。

![预览](./preview.jpg)

## 项目安装
```
npm install
```

### 调试模式
```
npm run serve
```

### 发布版本
```
npm run build
```

### 运行测试
```
npm run test
```

### 语法检查
```
npm run lint
```

### vue-cli的配置说明
See [Configuration Reference](https://cli.vuejs.org/config/).

## 在vue-cli的基础上加入对EarthSDK的支持的方法说明

1. 加入必须的包
```
npm install copy-webpack-plugin
npm install earthsdk
```

2. 创建vue.config.js
```javascript
// vue.config.js
const CopyWebpackPlugin = require('copy-webpack-plugin');

module.exports = {
  configureWebpack: config => {
    const cwp = new CopyWebpackPlugin([
      {
        from: './node_modules/earthsdk/dist/XbsjCesium',
        to: 'js/earthsdk/XbsjCesium',
        toType: 'dir'
      },
      {
        from: './node_modules/earthsdk/dist/XbsjEarth',
        to: 'js/earthsdk/XbsjEarth',
        toType: 'dir'
      },
    ]);
    config.plugins.push(cwp);
  }
}
```

3. 创建地球组件 EarthComp.vue
```html
<template>
  <div style="width: 100%; height: 100%">
    <div ref="earthContainer" style="width: 100%; height: 100%"></div>
    <div style="position: absolute; left: 18px; top: 18px">
      <button>{{ message }} 相机位置(经度/纬度/高度)：{{ position.map((e, i) => (e.toFixed(2))).join('/') }}</button>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
// 1 创建Earth的vue组件
var EarthComp = {
  data() {
    return {
      message: "页面加载于 " + new Date().toLocaleString(),
      _earth: undefined, // 注意：Earth和Cesium的相关变量放在vue中，必须使用下划线作为前缀！
      imageAlpha: 1.0,
      position: [0, 0, 0],
    };
  },
  // 1.1 资源创建
  mounted() {
    // 1.1.1 创建地球
    var earth = new XE.Earth(this.$refs.earthContainer);

    // 1.1.2 添加默认地球影像
    earth.sceneTree.root = {
      children: [
        {
          czmObject: {
            name: "默认离线影像",
            xbsjType: "Imagery",
            xbsjImageryProvider: {
              createTileMapServiceImageryProvider: {
                url: XE.HTML.cesiumDir + "Assets/Textures/NaturalEarthII",
                fileExtension: "jpg"
              },
              type: "createTileMapServiceImageryProvider"
            }
          }
        }
      ]
    };

    this._earth = earth;

    XE.MVVM.bindPosition(this, 'position', earth.camera, 'position');

    // 仅为调试方便用
    window.earth = earth;
  },
  // 1.2 资源销毁
  beforeDestroy() {
    // vue程序销毁时，需要清理相关资源
    this._earth = this._earth && this._earth.destroy();
  }
};

export default EarthComp;
</script>
```

4. 修改main.js，

需要等待earthsdk载入以后(XE.ready())，再创建vue的示例(new Vue(...))，代码如下：


```
import { createApp } from 'vue'
import App from './App.vue'

// createApp(App).mount('#app')
/* eslint-disable */
// XE.ready()会加载Cesium.js等其他资源，注意ready()返回一个Promise对象。
XE.ready().then(function startup() {
    createApp(App).mount('#app');
});
```


5. 再改改index.html文件中的css样式等

## 将此项目配置成使用纯Cesium开发

找到public/index.html文件，做如下修改，即可使用纯Cesium进行开发。

![](README_ASSETS/czm.png)