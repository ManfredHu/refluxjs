# RefluxJS

一个简单的被ReactJS启发的用于单向数据流框架的库
[Flux](http://facebook.github.io/react/blog/2014/05/06/flux.html).

[![NPM Version][npm-image]][npm-url]
[![NPM Downloads][downloads-image]][npm-url]
[![Bower Version][bower-image]][bower-url]
[![Dependencies][dependencies-image]][npm-url]
[![Build Status][travis-image]][travis-url]
[![Gratipay][gratipay-image]][gratipay-url]

[![Sauce Test Status](https://saucelabs.com/browser-matrix/refluxjs.svg)](https://saucelabs.com/u/refluxjs)

Development version: **0.4.x** ([release notes](https://github.com/reflux/refluxjs/issues?q=is%3Aclosed+label%3A%22release+notes%22))

You can read an overview of Flux [here](https://facebook.github.io/flux/docs/overview.html), however the gist of it is to introduce a more functional programming style architecture by eschewing MVC like pattern and adopting a single data flow pattern.

```
╔═════════╗       ╔════════╗       ╔═════════════════╗
║ Actions ║──────>║ Stores ║──────>║ View Components ║
╚═════════╝       ╚════════╝       ╚═════════════════╝
     ^                                      │
     └──────────────────────────────────────┘

```
这个模式由actions和data stores组成，actions初始化的新数据在视图组件回调之前传递给data stores,进而去改变视图组件.
如果视图组件有需要改变的事件在data stores，它需要通过actions发送去改变data stores

Feel free to open an issue on our [**discussion forum**](https://github.com/reflux/discuss) for **questions and general discussion**.  Here is a complete list of communication channels:

1. The [discussion forum](https://github.com/reflux/discuss)
2. [StackOverflow with the `refluxjs` tag](http://stackoverflow.com/questions/tagged/refluxjs)
3. `#flux` channel on Reactiflux Discord group. [Sign up here](https://discord.gg/0ZcbPKXt5bYedEbN) for an account.
4. [![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/spoike/refluxjs?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)
5. [![Thinkful][thinkful-image]][thinkful-url]

Please use the [issue tracker](https://github.com/spoike/refluxjs/issues) only for bugs and feature requests.

If you don't want to use the React-specific API, or want to develop Reflux for your view engine framework of choice, have a look at [`reflux-core`](https://github.com/reflux/reflux-core).

## Content

- [Comparing RefluxJS with Facebook Flux](#comparing-refluxjs-with-facebook-flux)
- [Examples](#examples)
- [Extensions and Plugins](#extensions-and-plugins)
- [Installation](#installation)
- [Usage](#usage)
     - [Actions](#creating-actions)
     - [Stores](#creating-data-stores)
     - [Component](#react-component-example)
- [Advanced Usage](#advanced-usage)
- [Colophon](#colophon)

## 对比 RefluxJS 和 Facebook的 Flux

reflux项目的目标是让你的web应用搭建起来更加简单便捷, 不管是在客户端还是在服务器端都是如此. 不同与Facebook的Flux，区别有下面几点:

You can read more in this [blog post about React Flux vs Reflux](http://spoike.ghost.io/deconstructing-reactjss-flux/).

### 与 Flux 相似但不相同

在 Reflux 有着一些与 Flux 相似的观点，如下:

* actions动作
* data stores数据存储
* 数据流动是单向的

### 与 Flux 的不同点

Reflux 已经重构了 Flux ，变得更加灵活和拥有函数运用 Functional Reactive Programming (FRP) 的特性:

* 单例分发器 dispatcher 被 action 取代
* 因为action是可监听的, 所以store也许监听了action. Stores 不必用一大段的 switch 语句去监测 action 的静态类别
* Stores 也许监听了其他的 stores, i.e. 它也许拥有可以合并数据的特性 *aggregate data further*, 类似数组 map/reduce 的东西.
* `waitFor` is replaced in favor to handle *serial* and *parallel* data flows:
 * **Aggregate data stores** (mentioned above) may listen to other stores in *serial*
 * **Joins** for joining listeners in *parallel*
* *Action creators* are not needed because RefluxJS actions are functions that will pass on the payload they receive to anyone listening to them

[Back to top](#content)

## 例子

You can find some example projects at these locations:

* [Todo Example Project](https://github.com/spoike/refluxjs-todo) - [http://reflux.github.io/refluxjs-todo/](http://reflux.github.io/refluxjs-todo/)
* [Hacker News Clone](https://github.com/echenley/react-news) by echenley
* [Another Todo Project with a Python backend](https://github.com/limelights/todo-reflux) by limelights
* [Sample app with authentication, permissions, sidebar and editable collection](https://github.com/VladimirPal/react-flux-backbone)
* [TodoMVC demonstrating Reflux + Angular](https://github.com/javamonn/Angular-TodoMVC-Redux)
* [Sample blog by @akornatskyy](https://github.com/akornatskyy/sample-blog-react)

[Back to top](#content)

## Extensions and Plugins

* [Cartiv](https://github.com/yonatanmn/cartiv) - new reactive interface based on Reflux
* [reflux-promise](https://github.com/reflux/reflux-promise) - Extends reflux with Promises
* [reflux-triggerable-mixin](https://github.com/jesstelford/reflux-triggerable-mixin) - Stores mixin adding `triggerable` syntax similar to `listenable`
* [reflux-state-mixin](https://github.com/yonatanmn/reflux-state-mixin) - Stores mixin adding `state` syntax similar to React components.

## Installation

You can currently install the package as a npm package or bower component.

### NPM

The following command installs reflux as a npm package:

    npm install reflux

### Bower

The following command installs reflux as a bower component that can be used in the browser:

    bower install reflux

### CDN

Reflux is available at [jsdelivr](http://www.jsdelivr.com/#!refluxjs).

### ES5

Reflux 像React 一样用了 es5-shim 来对付老式的浏览器. es5-shim.js [kriskowal's es5-shim](https://github.com/kriskowal/es5-shim)

[Back to top](#content)

## 用法

### 创建 actions

用 `Reflux.createAction` 创建action.可选的 options 对象为参数.

```javascript
var statusUpdate = Reflux.createAction(options);
```

action是一个可以被调用的函数对象，就像普通的对象一样 [function object](http://en.wikipedia.org/wiki/Function_object)

```javascript
statusUpdate(data); // Invokes the action statusUpdate
statusUpdate.triggerAsync(data); // same effect as above
```

如果`options.sync` 为 true, 函数调用就要用 `action.trigger` 这个同步选项. 你可以在action的生命周期内改变 `action.sync`, 跟着的调用也会一起改变

非常便捷的创建多个 actions 的方法，传入的为action数组.

```javascript
var Actions = Reflux.createActions([
    "statusUpdate",
    "statusEdited",
    "statusAdded"
  ]);

// Actions object now contains the actions
// with the names given in the array above
// that may be invoked as usual

Actions.statusUpdate();
```

#### 异步 actions

对于action的异步操作 (e.g. API calls) . 通常， 我们会在 completion 和 failure 即成功和失败注册函数，然后去执行. 对于这种需要注册回调函数的行为,你可以用`options.children`.

```javascript
// this creates 'load', 'load.completed' and 'load.failed'
var Actions = Reflux.createActions({
    "load": {children: ["completed","failed"]}
});

// when 'load' is triggered, call async operation and trigger related actions
Actions.load.listen( function() {
    // By default, the listener is bound to the action
    // so we can access child actions using 'this'
    someAsyncOperation()
        .then( this.completed )
        .catch( this.failed );
});
```

简短的调用 `completed` 和 `failed` 的方法: `options.asyncResult`. 下面的两种用法作用是一样的:

```javascript
createAction({
    children: ["progressed","completed","failed"]
});

createAction({
    asyncResult: true,
    children: ["progressed"]
});
```

#### Action hooks

There are a couple of hooks available for each action.

* `preEmit` - 在action被触发之前调用. 接受从action调用的参数.如果它返回的不是undefined, 它会被用作`shouldEmit`和 随后触发动作的参数.

* `shouldEmit` - 在 `preEmit` 之后，在action事件触发之前. 默认返回 `true` 会触发action. 你可以重写这个返回值如果你需要检查action接受的参数.

用例:

```javascript
Actions.statusUpdate.preEmit = function() { 
     console.log(arguments); 
};
Actions.statusUpdate.shouldEmit = function(value) {
    return value > 0;
};

Actions.statusUpdate(0);
Actions.statusUpdate(1);
// Should output: 1
```

当你创建对象的时候，你也可以通过设置默认对象 `hooks` 来创建action
You can also set the hooks by sending them in a definition object as you create the action:

```javascript
var action = Reflux.createAction({
    preEmit: function(){...},
    shouldEmit: function(){...}
});
```

#### Reflux.ActionMethods

如果你有一个通用的设置，你可以用 `Reflux.ActionMethods`对象，当被创建的时候会并入action里面

用例:

```javascript
Reflux.ActionMethods.exampleMethod = function() { console.log(arguments); };

Actions.statusUpdate.exampleMethod('arg1');
// Should output: 'arg1'
```

[Back to top](#content)

### 创建数据存储 data stores

创建一个data store数据存储就像 ReactJS 自带的 `React.createClass`方法一样简单，就是 `Reflux.createStore`这个方法，你也可以在`init`函数通过调用store的`listenTo`函数来设置action的监听器

```javascript
// Creates a DataStore
var statusStore = Reflux.createStore({
    // Initial setup
    init: function() {

        // Register statusUpdate action
        // this.listenTo(actionName, callback)
        this.listenTo(statusUpdate, this.output);
    },

    // Callback
    output: function(flag) {
        var status = flag ? 'ONLINE' : 'OFFLINE';

        // 传递给listeners
        this.trigger(status);
    }
});
```

上面的例子中, 无论何时action(statusUpdate)被调用了, store的 `output` 回调都将被调用，无论output的参数`flag`传入的是什么. 比如，当action被 `statusUpdate(true)` 调用，则在 `output`函数中的flag参数是`true`.

数据存储data store更像是一个publisher，所以拥有`preEmit` and `shouldEmit`两个hooks

#### Reflux.StoreMethods

如果有通用配置来设置store，你可以用 `Reflux.StoreMethods` 对象，它会在store被创建的时候传入配置

用例:

```javascript
Reflux.StoreMethods.exampleMethod = function() { 
     console.log(arguments); 
};

statusStore.exampleMethod('arg1');
// Should output: 'arg1'
```

#### Mixins in stores
就像你在React组件里用mixins一样，可以mixins到store里面

```javascript
var MyMixin = { //注意这里是一个对象
     foo: function() { 
          console.log('bar!'); 
     } 
}
var Store = Reflux.createStore({
    mixins: [MyMixin]
});
Store.foo(); // outputs "bar!" to console
```

mixins的对象的方法就像声明在store里面一样，所以可以用`this`来引用store里面的东西

```javascript
var MyMixin = { mixinMethod: function() { console.log(this.foo); } }
var Store = Reflux.createStore({
    mixins: [MyMixin],
    foo: 'bar!',
    storeMethod: function() {
        this.mixinMethod(); // outputs "bar!" to console
    }
});
```

一个非常棒的mixins的特点是如果在生命周期内，store有众多mixin，则所有的生命周期方法保证被调用。

#### Listening to many actions at once

监听所有的action是非常普遍的做法，我们可以用`listenToMany`函数来解决:

```javascript
var actions = Reflux.createActions(["fireBall","magicMissile"]);

var Store = Reflux.createStore({
    init: function() {
        this.listenTo(actions.fireBall,this.onFireBall); //这么写你会嫌烦的，看下面的写法
        this.listenTo(actions.magicMissile,this.onMagicMissile);
    },
    onFireBall: function(){
        // whoooosh!
    },
    onMagicMissile: function(){
        // bzzzzapp!
    }
});
```

你可以这么做:

```javascript
var actions = Reflux.createActions(["fireBall","magicMissile"]); //多个action

var Store = Reflux.createStore({
    init: function() {
        this.listenToMany(actions); //监听所有的action
    },
    onFireBall: function(){
        // whoooosh!
    },
    onMagicMissile: function(){
        // bzzzzapp!
    }
});
```

这会添加监听器来监听所有的相应的actions，那些`actionName`被相应的`onActionName`
This will add listeners to all actions `actionName` who have a corresponding `onActionName` (or `actionName` if you prefer) method in the store. Thus if the `actions` object should also have included an `iceShard` spell, that would simply be ignored.

#### The listenables shorthand

为了偷懒，如果你给store的`listenables` 属性设置actions,会自动用`listenToMany`.所以上面的例子可以简写成下面这样的:

```javascript
var actions = Reflux.createActions(["fireBall","magicMissile"]);

var Store = Reflux.createStore({
    listenables: actions,
    onFireBall: function(){
        // whoooosh!
    },
    onMagicMissile: function(){
        // bzzzzapp!
    }
});
```

 `listenables` 属性可以是一个对象数组,所有的参数会被传递到 `listenToMany`. 你可以这样:

```javascript
var Store = Reflux.createStore({
    listenables: [require('./darkspells'),require('./lightspells'),{healthChange:require('./healthstore')}],
    // rest redacted
});
```

#### Listenables and asynchronous actions

如果`options.children`被设置, 像下面这样，你可以用 `onActionSubaction` 添加监听到子action.

```javascript
var Actions = Reflux.createActions({
    "load": {children: ["completed", "failed"]}
});

function handleLoad(Action, Subaction){
    console.log("The on" + Action + Subaction + " handler was called");
};

var Store = Reflux.createStore({
    listenables: Actions,
    onLoad: function() {
        handleLoad("Load");
    },
    onLoadCompleted: function() {
        handleLoad("Load", "Completed");
    },
    onLoadFailed: function() {
        handleLoad("Load", "Failed");
    }
});
```


### Listening to changes in data store

组件里面可以在store监听状态的变化:

```javascript
// Fairly simple view component that outputs to console
function ConsoleComponent() {

    // Registers a console logging callback to the statusStore updates
    statusStore.listen(function(status) {
        console.log('status: ', status);
    });
};

var consoleComponent = new ConsoleComponent();
```

Invoke actions as if they were functions:

```javascript
statusUpdate(true);
statusUpdate(false);
```

With the setup above this will output the following in the console:

```
status:  ONLINE
status:  OFFLINE
```

[Back to top](#content)

### React component example

注册你的组件listen到你的data stores，最好在 `componentDidMount` [lifecycle method](http://facebook.github.io/react/docs/component-specs.html)绑定，在`componentWillUnmount`解绑：

```javascript
var Status = React.createClass({
    getInitialState: function() { },
    onStatusChange: function(status) {
        this.setState({
            currentStatus: status
        });
    },
    componentDidMount: function() { //绑定
        this.unsubscribe = statusStore.listen(this.onStatusChange);
    },
    componentWillUnmount: function() { //解除绑定
        this.unsubscribe();
    },
    render: function() {
        // render specifics
    }
});
```

#### Convenience mixin for React
你总是需要通过actions的观察者模式去取消订阅组件和删除stores上的数据，这个过程类似你用
[mixins in React](http://facebook.github.io/react/docs/reusable-components.html#mixins). There is a convenience mixin available at `Reflux.ListenerMixin`. Using that, the above example can be written like thus:

```javascript
var Status = React.createClass({
    mixins: [Reflux.ListenerMixin],
    onStatusChange: function(status) {
        this.setState({
            currentStatus: status
        });
    },
    componentDidMount: function() {
        this.listenTo(statusStore, this.onStatusChange);
    },
    render: function() {
        // render specifics
    }
});
```

The mixin provides the `listenTo` method for the React component, that works much like the one found in the Reflux's stores, and handles the listeners during mount and unmount for you. You also get the same `listenToMany` method as the store has.


#### Using Reflux.listenTo

If you're not reliant on any special logic for the `this.listenTo` calls inside `componentDidMount`, you can instead use a call to `Reflux.listenTo` as a mixin. 自动设置`componentDidMount`搞定剩下的,例如添加`ListenerMixin` 功能. 

```javascript
var Status = React.createClass({ //创建组件
    mixins: [Reflux.listenTo(statusStore,"onStatusChange")],
    onStatusChange: function(status) {
        this.setState({
            currentStatus: status
        });
    },
    render: function() {
        // render using `this.state.currentStatus`
    }
});
```

You can have multiple calls to `Reflux.listenTo` in the same `mixins` array.

There is also `Reflux.listenToMany` which works in exactly the same way, exposing `listener.listenToMany`.

#### Using Reflux.connect

如果你想要数据一更新组件就更新，你可以用`Reflux.connect(listener,stateKey)`作为mixins.
它会通过 `this.setState({<stateKey>:data})`搞定.例子如下:

```javascript
var Status = React.createClass({
    mixins: [Reflux.connect(statusStore,"currentStatus")],
    render: function() {
        // render using `this.state.currentStatus`
    }
});
```

`Reflux.connect()` mixin 会监测store的 `getInitialState` 方法. 如果找到它会设置组件的 `getInitialState`

```javascript
var statusStore = Reflux.createStore({
    getInitialState: function() {
        return "open";
    }
});

var Status = React.createClass({
    mixins: [Reflux.connect(statusStore,"currentStatus")],
    render: function() {
        // render using `this.state.currentStatus`
        // this.state.currentStatus === "open"
    }
});
```

#### Using Reflux.connectFilter

`Reflux.connectFilter` is used in a similar manner to `Reflux.connect`. Use the
`connectFilter` mixin when you want only a subset of the items in a store. A
blog written using Reflux would probably have a store with all posts in
it. For an individual post page, you could use `Reflux.connectFilter` to
filter the posts to the post that's being viewed.

```javascript
var PostView = React.createClass({
    mixins: [Reflux.connectFilter(postStore, "post", function(posts) {
        return posts.filter(function(post) {
           return post.id === this.props.id;
        }.bind(this))[0];
    })],
    render: function() {
        // render using `this.state.post`
    }
});
```

### Listening to changes in other data stores (aggregate data stores)

A store may listen to another store's change, making it possible to safely chain stores for aggregated data without affecting other parts of the application. A store may listen to other stores using the same `listenTo` function as with actions:

```javascript
// Creates a DataStore that listens to statusStore
var statusHistoryStore = Reflux.createStore({
    init: function() {

        // Register statusStore's changes
        this.listenTo(statusStore, this.output);

        this.history = [];
    },

    // Callback
    output: function(statusString) {
        this.history.push({
            date: new Date(),
            status: statusString
        });
        // Pass the data on to listeners
        this.trigger(this.history);
    }

});
```

[Back to top](#content)

## Advanced usage

### Switching EventEmitter

Don't like to use the EventEmitter provided? You can switch to another one, such as NodeJS's own like this:

```javascript
// Do this before creating actions or stores

Reflux.setEventEmitter(require('events').EventEmitter);
```

### Switching nextTick

Whenever action functors are called, they return immediately through the use of `setTimeout` (`nextTick` function) internally.

You may switch out for your favorite `setTimeout`, `nextTick`, `setImmediate`, et al implementation:

```javascript

// node.js env
Reflux.nextTick(process.nextTick);
```

For better alternative to `setTimeout`, you may opt to use the [`setImmediate` polyfill](https://github.com/YuzuJS/setImmediate), [`setImmediate2`](https://github.com/Katochimoto/setImmediate) or [`macrotask`](https://github.com/calvinmetcalf/macrotask).


### Joining parallel listeners with composed listenables

The Reflux API contains `join` methods that makes it easy to aggregate publishers that emit events in parallel. This corresponds to the `waitFor` method in Flux.

#### Argument tracking

A join is triggered once all participating publishers have emitted at least once. The callback will be called with the data from the various emissions, in the same order as the publishers were listed when the join was created.

There are four join methods, each representing a different strategy to track the emission data:

*    `joinLeading`: Only the first emission from each publisher is saved. Subsequent emissions by the same publisher before all others are finished are ignored.
*    `joinTrailing`: If a publisher triggers twice, the second emission overwrites the first.
*    `joinConcat`: An array of emission arguments are stored for each publisher.
*    `joinStrict`: An error is thrown if a publisher emits twice before the join is completed.

The method signatures all look like this:

```javascript
joinXyz(...publisher,callback)
```

Once a join is triggered it will reset, and thus it can trigger again when all publishers have emitted anew.

#### Using the listener instance methods

All objects using the listener API (stores, React components using `ListenerMixin`, or other components using the `ListenerMethods`) gain access to the four join instance methods, named after the argument strategy. Here's an example saving the last emission from each publisher:

```javascript
var gainHeroBadgeStore = Reflux.createStore({
    init: function() {
        this.joinTrailing(actions.disarmBomb, actions.saveHostage, actions.recoverData, this.triggerAsync);
    }
});

actions.disarmBomb("warehouse");
actions.recoverData("seedyletter");
actions.disarmBomb("docks");
actions.saveHostage("offices",3);
// `gainHeroBadgeStore` will now asynchronously trigger `[["docks"],["offices",3],["seedyletter"]]`.
```

#### Using the static methods

Since it is rather common to have a store where the only purpose is to listen to a join and trigger when the join is completed, the join methods have static counterparts on the `Reflux` object which return stores listening to the requested join. Using them, the store in the example above could instead be created like this:

```javascript
var gainHeroBadgeStore = Reflux.joinTrailing(actions.disarmBomb, actions.saveHostage, actions.recoverData);
```

### Sending initial state with the listenTo function

The `listenTo` function provided by the `Store` and the `ListenerMixin` has a third parameter that accepts a callback. This callback will be invoked when the listener is registered with whatever the `getInitialState` is returning.

```javascript
var exampleStore = Reflux.createStore({
    init: function() {},
    getInitialState: function() {
        return "the initial data";
    }
});

// Anything that will listen to the example store
this.listenTo(exampleStore, onChangeCallback, initialCallback)

// initialCallback will be invoked immediately with "the initial data" as first argument
```

Remember the `listenToMany` method? In case you use that with other stores, it supports `getInitialState`. That data is sent to the normal listening callback, or a `this.on<Listenablename>Default` method if that exists.

[Back to top](#content)

## Colophon

[List of contributors](https://github.com/spoike/reflux/graphs/contributors) is available on Github.

This project is licensed under [BSD 3-Clause License](http://opensource.org/licenses/BSD-3-Clause). Copyright (c) 2014, Mikael Brassman.

For more information about the license for this particular project [read the LICENSE.md file](LICENSE.md).

This project uses [eventemitter3](https://github.com/3rd-Eden/EventEmitter3), is currently MIT licensed and [has it's license information here](https://github.com/3rd-Eden/EventEmitter3/blob/master/LICENSE).

[npm-image]: http://img.shields.io/npm/v/reflux.svg
[downloads-image]: http://img.shields.io/npm/dm/reflux.svg
[dependencies-image]: http://img.shields.io/david/reflux/refluxjs.svg
[npm-url]: https://www.npmjs.org/package/reflux
[bower-image]: http://img.shields.io/bower/v/reflux.svg
[bower-url]: http://bower.io/search/?q=reflux
[travis-image]: http://img.shields.io/travis/reflux/refluxjs/master.svg
[travis-url]: https://travis-ci.org/reflux/refluxjs
[gratipay-image]: http://img.shields.io/gratipay/spoike.svg
[gratipay-url]: https://gratipay.com/spoike/
[thinkful-image]: https://tf-assets-staging.s3.amazonaws.com/badges/thinkful_repo_badge.svg
[thinkful-url]: http://start.thinkful.com/react/?utm_source=github&utm_medium=badge&utm_campaign=reflux
