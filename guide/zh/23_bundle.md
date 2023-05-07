# 资源包
<!-- bundle -->

`ThingJS`除了直接加载文件外，还可以加载资源包`Bundle`。资源包是一个包含了`bundle.json`的文件夹，这个文件夹可以存储单个场景、蓝图、效果、插件、预制件等各种类型的资源，也可以存储多个不同类型的文件组合。资源包的主要作用是资源的统一管理，以及对多种资源文件的组合使用。

## 描述文件
每个资源包都包含了`bundle.json`，例如：
```javascript
{
    "name": "scene",
    "id": "D3752018F4554EA4835447011318A757",
    "version": "2.0.0",
    "type": "scene",
    "files": [
        // 场景文件
        {
            "name": "MainScene",
            "url": "./main.json",
            "type": "scene" // 文件类型
        }, 
        // 场景文件
        {
            "name": "SubScene",
            "url": "./sub.json",
            "type": "scene"
        },
        // 渲染设置文件
        {
            "url": "./rendersettings.json",
            "type": "rendersetting"
        }
    ],
    
    // 扩展
    "extensions": {
    }
  }
```

资源包的`type`字段指定了这个资源包的类型，用于表明如何使用这个资源包内的文件，如：`scene`、`theme`、`plugin`等等。`files`中的`type`指定的是这个文件的类型，用于表明使用哪个加载器来加载这个文件。

当资源包只有一个文件时，可以用`file`字段来自定文件，同时`type`指定的则是这个文件的类型：
```javascript
{
    "name": "myscene",
    "id": "D3752018F4554EA4835447011318A757",
    "version": "2.0.0",
    "type": "scene",
    "file": "./main.json" // 兼容之前的main？
}
```
## 加载资源包
配置好`bundle.json`后，使用`app.load(url)`指定资源包的路径就可以加载，返回值为一个`bundle`对象`{}`。

```javascript
let bundleURL = "./bundles/bundle01";

// await 的方式，等待加载完成
let bundle = await app.load(bundleURL);
console.log( bundle.info );

// 或 then 的方式，等待加载完成后回调
app.load(bundleURL).then((ev) => {
    console.log(ev.info);
})；
```


