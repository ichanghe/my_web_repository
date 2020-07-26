# electron入门知识整理

## 1、electron介绍

- Electron的原理：Electron 通将 Chromium 和 Node.js 合并到同一个运行时环境中，并将其打包为 Mac，Windows 和 Linux 系统下的应用；
- Chromium是一个谷歌开源浏览器，可以调用所有前端相关的API，且不需要考虑兼容性问题；

- 浏览器并不能访问原生的资源，但Node.Js的API支持在页面中和操作系统进行交互。

## 2、创建最简单的helloworld

- 克隆github上从仓库

- 手动搭建

## 3、常用的app事件

- ready ：当Electron完成初始化时被触发。
- window-all-close：所有窗口被关闭。
- before-quit：在应用程序开始关闭窗口之前触发。
- will-quit：当所有窗口都已经关闭并且程序将退出时发出。
- quit：在用用程序退出时发出。

## 4、webContents常用事件

- did-finish-load：窗口加载完毕
- openDevTools:打开调试
- send：主动给窗口发送事件

## 5、process进程对象

- process.platform //获取运行平台
- process.cwd() //
储存应用数据时，通常会使用 应用程序所在目录。即 userData 目录。
路径是这样的：C:\Users\【用户名】\AppData\Roaming\【应用名】
可通过以下方法获取：electron.app.getPath('userData')
但某些情景。我希望某些数据存放在 打包后的当前路径 下。
即 应用名.exe 的同级目录下。

## 6、BrowserWindow

```javascript

new BrowserWindow({
  width: 960,
  minWidth: 960,
  height: 600,
  fullscreen: false,
  titleBarStyle: "hiddenInset", //交通灯
  frame: false, //无边框设置
  webPreferences: {
    nodeIntegration: true,
  },
});

```

- nodeIntegration //是否集成Node,很重要
- frame:false：无边框化，去除顶部菜单、最小化、最大化和关闭；
let win = new BrowserWindow({..., frame:false})
- 无边框化之后，窗口的可拖拽区也随之消失，需要手动设置拖拽区，鼠标可以拖动窗口；
- 设置拖拽区的CSS属性：-webkit-app-region: drag
body{ -webkit-app-region: drag } --> 设置整个窗口为拖拽区
- win.setMenu(null)：隐藏顶部菜单；
- 最大化、最小化、隐藏、关闭窗口
win.hide()：隐藏窗口； win.isVisible()：窗口是否可见； win.close()：关闭窗口
win.maximize()：最大化窗口，如果窗口尚未显示，这也将会显示，但不会有焦点；
win.unmaximize()：取消窗口最大化； win.isMaximized()：判断窗口是否最大化；
win.minimize()：最小化窗口；
win.isMinimized()：判断窗口是否最小化；
win.restore()：将窗口从最小化状态恢复到以前的状态

## 7、对话框

dialog模块：不仅可以弹出信息提示框，也可以实现本地文件的打开与保存；
打开文件/目录：dialog.showOpenDialog({}, (path) => {...})

- 打开功能只是回调文件/目录的路径，并不能获取文件/目录的内容；
- 操作文件/目录的功能，需要通过nodejs实现。

```javascript
// 打开epc文件
ipcMain.on("open-epc", (event) => {
  dialog
    .showOpenDialog({
      properties: ["openFile"],
      filters: [{ name: "epc文件", extensions: ["epc"] }],
    })
    .then((res) => {
      selectBook(event, res.filePaths);
    });
})

```

## 8、主进程与子进程通讯

- 被Electron直接运行的脚本(package.json中指定的main节点)称为主进程，有且只有一个；
- 用于展示的web界面都运行在一个独立的进程中，称为渲染进程；
- 进程间通信的相关模块：ipcMain(主进程)、ipcRenderer(渲染进程)
- 渲染进程发送消息到主进程
1.　渲染进程向主进程发送异步消息：ipcRenderer.send('事件名1', 消息内容)
2.　主进程中监听消息：ipcMain.on('事件名1', (event, arg) => { ... })
3.　主进程给渲染进程回复消息：event.sender.send('事件名2', 消息内容)
4.　渲染进程监听回复的消息：ipcRenderer.on('事件名2', (event, arg) => {})
- 渲染进程向主进程发送同步消息：(异步已经够用)
1.let msg = ipcRenderer.sendSync('事件名1', 消息内容)
2.与异步消息不同，主进程收到同步消息，必须回复，否则渲染进程会阻塞；
ipcMain.on('事件名1', (event, arg) => {
event.returnValue = 'sync reply' ---> 回复给渲染进程
}) ----> sendSync()的返回值就是主进程回复的消息内容

## 9、文件路径

- electron.app.getPath('home'); // 获取用户根目录
- electron.app.getPath('userData'); // 用于存储 app 用户数据目录
- electron.app.getPath('appData'); // 用于存储 app 数据的目录，升级会被覆盖
- electron.app.getPath('desktop'); // 桌面目录

## 10、数据持久化electron-store

```javascript
const Store = require('electron-store');

const myStore = new Store ({
  name: "Book Data",
  cwd: process.cwd(), //文件位置,尽量不要动
  //    encryptionKey:"aes-256-cbc" ,//对配置文件进行加密
  clearInvalidConfig: true, // 发生 SyntaxError  则清空配置,
});

myStore .set('unicorn', ');
console.log(myStore.get('unicorn'));
// 使用点表示法访问嵌套属性
myStore.set('foo.bar', true);
console.log(myStore.get('foo'));
myStore .delete('unicorn');
console.log(myStore.get('unicorn'));

```

## 11、应用打包

```json

 "dist_win": "electron-builder --ia32 --win",
 "dist_mac": "electron-builder --mac",
 "dmg": {
      "icon": "./dist/icon.icns",
      "contents": [
        {
          "x": 380,
          "y": 280,
          "type": "link",
          "path": "/Applications"
        },
        {
          "x": 110,
          "y": 280,
          "type": "file"
        }
      ],
      "window": {
        "width": 500,
        "height": 500
      }
    },
    "win": {
      "icon": "./dist/icon.ico",
      "target": [
        {
          "target": "nsis",
          "arch": [
            "ia32"
          ]
        }
      ]
    }
```
