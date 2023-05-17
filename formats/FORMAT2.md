[TOC]

# 场景格式
<!-- bundle -->

`ThingJS`的场景格式，是用于描述数字孪生场景中的对象、关系、集合、效果、行为的`JSON`文件格式。

![TSF](./images/tsf.png "TSF")

## 格式说明
下面是一个场景文件`JSON`的大体结构：
```javascript
{
  // 头信息
  "name": "scene01",
  "id": "972c819-ee02-11ed-bfbe-c0b5d78a0456",
  "version": "2.0",
  "description": "ThingJS Scene Format",
  "author": "uino",
  // 对象
  "objects": [],
  // 关系
  "relationships": [],
  // 渲染设置
  "rendersettings": {},
  // 效果
  "styles": [],
  // 蓝图
  "blueprints": [],
  // 选择集
  "selections": [],
  // 引入其他场景文件
  "files": [],
  // 插件
  "plugins": [],
  // 脚本
  "scripts": [],
  // 扩展
  "extensions": {},
  // 预先下载文件
  "preDownloads": []
}
```
一个场景文件可以存储上述的全部字段内容，也可以只存储其中的部分字段。只存储部分字段的文件，一般用于描述某类数据，比如：只存储效果相关的字段，当加载这个文件仅用于生效某个效果主题。

兼容字段：为了和1.0版本的文件兼容，支持`type`字段，代表本文件的类型，以及`main`字段，作为单一文件的入口，即使用`main`时候，`files`不在生效。

## 加载和导出

## 组合使用
`ThingJS`在实际应用中，经常通过一个场景文件，引用多个场景文件组合使用，例如：
```javascript
// bundle.json
{
    "name": "MyScene",
    "files": [
        "./main.json", // 主场景
        "./uino.gltf", // gltf场景
        "./deploys.json", // 部署对象
        "./rendersettings.json", // 渲染设置文件
        "./blueprint.json", // 蓝图文件
    ]
}
```
使用`app.load`方法加载文件：
```javascript
// 加载场景文件
let bundle = await app.load("./scenes/uino/bundle.json");

// 加载到的场景根对象
console.log(bundle.root);

// 加载到的蓝图
console.log(bundle.blueprints[0]);
```
在加载这个`bundle.json`文件后，就可以获取到多个文件中的的对象、关系、渲染设置、和蓝图等，并立即生效。


## 效果主题
可以将效果相关的文件放到一起，组成一个效果主题包，如:

```javascript
// back-to-earth.json
{
    "name": "MyTheme",
    "files": [
        "./rendersettings.json", // 渲染设置：天空盒，灯光，后期等
        "./styles.json", // 物体效果设置
        "./effect-grounds.json", // 特效地面设置
        "./effect-particle.json", // 粒子效果
    ]
}
```

> 注意，效果主题包，通常会用于不同主题的切换，所以一般不希望立即被应用到场景中，只有当需要时候才应用，以达到切换到效果的目的，需要传入`apply`参数为`false`。

```javascript
// 加载，但不立即生效
let bundle = await app.load("./themes/theme.json", {apply: false});

// 在需要的时候，让主题生效
bundle.apply();
```

## 预制件
预制件文件，一般是包含了对象字段，以及对组件脚本、蓝图脚本引用的文件，例如：
```javascript
// spaceman.json
{
  "name": "spaceman",
  // 对象
  "objects": [{
    ...
  }],
  // 脚本
  "scripts": [
    "./comp.js", // 组件脚本
    "./bpnode.js", // 蓝图节点文件    
  ]
}
```

可以通过创建一个`Entity`对象，来加载预制件：
```javascript
// 实例化预制件
let obj = new THING.Entity({
    url: "./prefabs/spaceman.json"
});
```

