### 使用typescript开发微信小程序

#### 新建项目

- 使用 ==微信开发者== 工具在创建项目时，在语言选选项勾选`typescript`。
- 创建项目完成后，在`详情 > 本地配置 `中勾选==启用自定义处理命令==，这样我们就可以只关注项目中的`index.ts`文件，编译后既可以生成对应得`index.js`文件。

####  开启tsc监视

- 为了可以使得我们在保存ts文件时自动生成js文件，我们可以选用VS Code编辑器打开项目文件，在`终端 > 运行任务 > typescript > tsc:监视 `。这样就可以自动生成对应得js文件，极大提高我们的开发效率

#### 使用npm构建

- 为了进一步的提高我们的开发效率，我们可能需要在项目中使用npm，这样我们就可以使用一些成熟好用的npm工具包和UI组件库。

- 首先我们可以看到初始文件目录为：

  ```
  .
  |-- miniprogram
  |-- node_modules 
  |-- typings  
  |-- project.config.json  
  |-- package.json  
  |-- tsconfig.json 
  ```

- 我们需要先把`package.json`移动到`miniprogram`下，并且使用终端进入到`miniprogram`目录下，==运行`npm i`和`npm i miniprogram-sm-crypto --production`两个命令==，同时删除根目录下的`node_modules`。

- 接下来我们需要进入微信开发者工具，在`详情 > 本地配置 `中勾选==使用npm模块==，再在点击`工具 > 构建npm `既可完成npm构建。

- 这样我们就可以使用安装下来的npm包，但是记得在使用前需要再点击`工具 > 构建npm `（对于新安装的npm工具包）。

- 相关注意事项可见[官网](https://developers.weixin.qq.com/miniprogram/dev/devtools/npm.html)，注意：在ts项目中按照官网中的步骤构建npm，可能会出错。

#### 使用第三方组件库（以vant组件库为例）

- 首先使用`npm i @vant/weapp -S --production`安装。

- 去掉`app.json`中的` "style": "v2"` 

- 修改`project.config.json`如下：

  ```json
  {
    ...
    "setting": {
      ...
      "packNpmManually": true,
      "packNpmRelationList": [
        {
          "packageJsonPath": "./miniprogram/package.json",  // 一定要适应自己项目的结构
          "miniprogramNpmDistDir": "./miniprogram/"
        }
      ]
    }
  }
  ```

- 在微信小程序中构建npm，既可使用vant组件。具体使用方法见[官方文档](https://vant-contrib.gitee.io/vant-weapp/#/home)。注意：我的步骤可能和官网的步骤不一样，也是因为官网的有些步骤有点多余，且可能无法适应小程序新的文档结构。

#### 注意事项：

- 因为改变了文档的结构，tsc监视会变慢很多，我们需要修改`tsconfig.json`中的`exclude`选项去排除监视`node_modules`里文件。

