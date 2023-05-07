# 资源包
<!-- bundle -->

`ThingJS`除了直接加载文件外，还可以加载资源包`Bundle`。资源包是一个包含了`bundle.json`的文件夹，这个文件夹可以存储单个场景、蓝图、效果、插件、预制件等各种类型的资源，也可以存储多个不同类型的文件**组合**。资源包的主要作用是资源的统一管理，以及对多种资源文件的组合使用。

## 描述文件
每个资源包都包含了`bundle.json`。其中`type`字段是必填字段，它指定了这个资源包的类型，用于说明如何使用这个资源包内的文件，如：`scene`、`theme`、`plugin`等等。

资源包的`files`中的`type`指定的是这个文件的类型，用于表明使用哪个加载器来加载这个文件。

```javascript
{
    "name": "MyScene",
    "id": "60cb90d0-eca2-11ed-b4c3-c0b5d78a0456",
    "version": "2.0.0",
    "type": "scene", // 资源包类型，用于指定如何使用包内资源
    "files": [
        // 场景文件
        {
            "name": "MainScene",
            "url": "./main.json",
            "type": "scene" // 文件类型，用于指定用哪个加载器加载这个文件
        },
        // 场景文件
        {
            "name": "SubScene",
            "url": "./sub.json",
            "type": "scene"
        }
    ],
    // 扩展
    "extensions": {
    }
}
```

当资源包只有一个文件时，可以用`main`字段来指定文件，同时`type`所指定的，则是这个文件的类型：
```javascript
{
    "type": "scene",
    "main": "./main.json" 
    // ？"file": "./main.json" 
}
```

其他还有一些可选字段，如：
* `name` 资源包的名称
* `id` 资源包的编号
* `version` 资源包的版本
* `author` 资源包作者
* `description` 资源包描述
* `extensions` 可用于填写用户的扩展内容

## 加载资源包
配置好`bundle.json`后，使用`app.load(url)`指定资源包的路径就可以加载，返回值为一个`bundle`对象`{}`。

```javascript
let bundleURL = "./bundles/bundle01";

// await 的方式，等待加载完成
let bundle = await app.load(bundleURL);
console.log( bundle.name );

// 或 then 的方式，等待加载完成后回调
app.load(bundleURL).then((ev) => {
    console.log(ev.name);
})；
```

## 资源包举例

### 场景包
场景资源包的类型为`scene`，`files`中可以包含一个或多个场景文件、渲染设置文件等，例如：
```javascript
{
    "name": "MyScene",
    "type": "scene",
    "files": [
        // 场景文件1
        {
            "name": "MainScene",
            "url": "./main.json",
            "type": "scene"
        },
        // 场景文件2
        {
            "name": "SubScene",
            "url": "./sub.json",
            "type": "scene"
        },
        // gltf文件
        {
            "name": "UinoScene",
            "url": "./uino.gltf",
            "type": "scene"
        },
        // 渲染设置文件
        {
            "url": "./rendersettings.json",
            "type": "rendersetting"
        }
    ]
}
```

> 注意，在场景包中，场景文件会被直接加载到场景中，渲染设置等文件也会被立即应用。

```javascript
// 加载一个场景包
let bundle = await app.load("./scenes/uino");

// 资源包唯一的根对象
console.log(bundle.object); // ？

// 资源包加载的多个根对象
console.log(bundle.objects); // ？
```
？建筑内场景加载

### 效果主题包
效果主题资源包，包含了渲染设置、物体效果设置和一些特效地面、粒子效果等物体组成的场景文件，例如：
```javascript
{
    "name": "MyTheme",
    "type": "theme",
    "files": [
        // 存储全局环境配置，目前包括天空盒，灯光，后期
        {
            "url": "./rendersettings.json",
            "type": "renderSetting"
        }, 
        // 物体Style效果的配置文件，基于标签分类配置
        {
            "url": "./styles.json",
            "type": "style"
        }, 
        // 特效地面的配置文件
        {
            "url": "./grounds.json",
            "type": "scene" // ？
        }, 
        // 粒子效果的配置文件。
        {
            "url": "./particle.json",
            "type": "scene"// ？
        }
    ]
}
```

> 注意，效果主题资源包，通常会用于不同主题的切换，所以内容不会立即被加载到场景中，需要你手动应用，以达到切换到效果。
> 
```javascript
// 加载一个场景包
let bundle = await app.load("./themes/back-to-earth");

// 返回到效果主题对象
console.log(bundle.theme); 

// 主题生效
bundle.theme.apply(); // ？
```


### 插件包
插件资源包，一般只包含一个插件的入口文件，例如：
```javascript
{
	"name": "MyPlugin",
	"type": "plugin",
	"main": "index.js",
	"dependencies": {
		"thingjs": ">=2.0 <2.5" // 可以指定插件以来的版本，暂未支持
	}
}
```

> 注意，加载插件资源包，会立即调用插件的`onInstall`，让插件立即生效，如果插件需要一些构造参数，请在`load`的参数中传入
> 
```javascript
// 加载插件包
let bundle = await app.load('plugins/my-plugin', { speed: 5 });

// 获取插件实例
// bundle[bundle.info.name]; ？
let myPlugin = bundle.plugin;
myPlugin.sayHello();

// 使用插件包中导出的类型
let cabinet = new bundle.Cabinet();

let building = new bundle.Building();
let floor = new bundle.Floor();
```

### 预制件包
预制件资源包，一般包含一个预制件所需的场景文件，以及组件文件或蓝图节点文件，例如：
```javascript
{
  "name": "Spaceman",
  "type": "prefab",
  "files": [
        // 场景文件
        {
            "url": "prefab.json",
            "type": "scene"
        },
        // 组件脚本
        {
            "url": "index.js",
            "type": "script"
        },
        // 蓝图节点文件
        {
            "url": "blueprint.js",
            "type": "script"
        }
  ]
}
```

```javascript
// 实例化预制件
let obj = new THING.Entity({
    url: "./prefabs/spaceman"
});
```

