# 插件
<!-- plugin -->
`ThingJS`引擎的插件`Plugin`是对系统功能的扩展。用户可以在插件中写自定义代码、使用组件、引用模型/图片等资源内容，对一个独立功能进行封装和复用。比如：给 `ThingJS` 增加物理系统、寻路系统等。每个插件在`app`中一般只注册一个。

## 插件类
开发一个插件，需要继承`BasePlugin`类，下面是一个插件类的例子：
```javascript
// 插件
class MyPlugin extends THING.BasePlugin {
    constructor(params) {
      super()
      this.box = null;        
      // 常用成员：this.app、this.camera;
    }
    // 插件方法
    sayHello() {
      console.log("hello MyPlugin!");
    }    
    // 插件安装完成
    onInstall(options) {
        // 创建一个Box
        this.box = new THING.Box();

        // 注册需要的组件等
        this.box.addComponent(MyComponent, options);        
    }
    // 插件卸载完成
    onUninstall() {
      this.box.removeComponent(MyComponent);
      this.box.destroy();  
      this.box = null;    
    }

    // 系统初始化之前
    onBeforeInit() {
    }
    // 系统初始化完成
    onInited() {
    }
    // 场景加载之前
    onBeforeLoad() {
    }
    // 场景加载完成
    onLoaded() {
    }
    // 更新事件
    onUpdate() {
    }
}
```

可以通过`app.install()`来直接安装插件：
```javascript
// 安装插件
app.install(new MyPlugin({ speed: 10 }), "myPlugin");
// 已经安装的插件
const plugin = app.plugins['myPlugin'];
// 卸载插件
app.uninstall("myPlugin");
```
但常见的加载插件的方式是直接加载插件包，详见下面的文档。

## 开发插件
可通过CLI创建一个插件包：

## 加载插件
引擎通过`app.load()`方法来加载插件：
```javascript
let url = "./plugins/myPlugin"; // ？
let params = {};

// await 的方式，等待加载完成
let bundle = await app.load(url, params);
bundle.plugin.sayHello();

// 或 then 的方式，等待加载完成后回调
app.load(url, params).then((ev) => {
    ev.plugin.sayHello();
})；
```

## 导出成员
为了导出插件中的属性或方法，参考`jsdoc`中`@`关键字的写法，通过脚手架打包编译，导出插件属性。

* `@public` 表示是否暴露该属性或者方法
* `@widgetType {type}` 属性类型，type 值对应在 `property-panel` 上的类型
* `@widgetAlias` 别名，例如：“移动”就是“move”的别名，也可充当翻译功能
* `@widgetParam {type}` `[xx=xx]property-panel`控件的配置项说明，例如：滑动条方法只需写`@public`即可
* `jsdoc`中的类型也是可以复用，比如`@description`等

举例：
```javascript
class Navigation extends THING.BasePlugin {
  
  constructor() {
    super()
        
    /**
     * @public
     * @widgetAlias 速度
     * @widgetType {slider}
     * @widgetParam {number} [min=0]
     * @widgetParam {number} [max=5]
     * @widgetParam {number} [step=0.1]
     */
    this.speed = 0.1

    // 是否循环
    this.loop = true

    // 路径
    this.path = []
  }
  
  /**
   * 移动
   * @public
   * @description 移动的描述
  */
  move() {
    this.box.movePath(this.path, {
      time: this.speed > 0.1 ? 1000 : 2000,
      times: this.loop ? Infinity : 1,
    })
  }
  
  onInstall(options) {
    this.box = new THING.Box();
    this.speed = props.speed || this.speed;
  }
}
```
这样的话，属性speed和方法move就被暴露出去，暴露出去的数据可以通过插件的实例上的`plugin.props`获取到。

## 导出类
很多时候我们的业务需要自定义一些类（例如机柜类、车类、人类等），然后将这些类交给各个应用自己去实例。

例如，一个自定义机柜类：
```javascript
// scripts/Cabinet.js
export default class Cabinet extends THING.Object3D {
  constructor() {
    super();
  }
  createRacks() {
    // ...
  }
  destroyRacks() {
    // ...
  }
}
```

在插件中导入自定义类：
```javascript
import Cabinet from 'scripts/Cabinet.js';
import Car from 'scripts/Car.js';
import Person from 'scripts/Person.js';

class MyPlugin extends Thing.BasePlugin {
  ...
}

// 导出自定义类
export { Cabinet, Car, Person }
```

加载插件后，使用插件中的类型：
```javascript
// await 的方式，等待加载完成
let bundle = await app.load(url, params);
let cabinet = bundle.Cabinet();
cabinet.createRacks();
```

