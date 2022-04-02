### 小程序的分包（uni-app 下）

###### 应用场景

> 由于小程序的代码包大小限制在 2m 以内。所以当我们开发较大工程时，需要用到小程序的分包功能，去满足我们的需求。
>
> 相关文档见：[uni-app](https://uniapp.dcloud.io/collocation/pages?id=subpackages)、[微信小程序](https://developers.weixin.qq.com/miniprogram/dev/framework/subpackages/basic.html)。

###### 实现步骤

> 1.  在工程的根目录下创建相应的分包页，即下面的`roots`对应项。
>
> 2.  在`pages.json`下填写类似如下的配置：
>
> ```json
>    "subPackages": [{
>            "root": "pagesA",
>            "pages": [{
>                "path": "list/list",
>                "style": { ...}
>            }]
>        }, {
>            "root": "pagesB",
>            "pages": [{
>                "path": "detail/detail",
>                "style": { ...}
>            }]
>        }]
> ```
>
> 3.  如果在 uniapp 下开发，需要在`manifest.json`文件下`"mp-weixin"`下配置(==否则 uniapp 会将分包下的 js 文件编译到主包里==)：
>
> ```json
> "optimization": {
>    "subPackages": true
> },
> ```

### 小程序的自定义 tabbar

###### 应用场景

> 当小程序原生的 tabbar 组件无法满足我们的需求时，或者因为上面的分包需要将一个 tabbar 页面分出，这时我们就需要自定义 tabbar，将 tabbar 的页面入口改写为一个按钮。
>
> [参考文档](https://developers.weixin.qq.com/miniprogram/dev/framework/ability/custom-tabbar.html)

###### 实现步骤

> 1. 在项目的根目录创建`coustom-tab-bar`目录，并在目录下创建`index.js`、`index.wxml`、`index.wxss`、`index.json`四个文件。
>
> 2. 在`pages.json`里声明配置：
>
>    ```json
>    {
>      "tabBar": {
>        "custom": true,
>        "color": "#000000",
>        "selectedColor": "#000000",
>        "backgroundColor": "#000000",
>        "list": [
>          {
>            "pagePath": "page/component/index",
>            "text": "组件"
>          },
>          {
>            "pagePath": "page/API/index",
>            "text": "接口"
>          }
>        ]
>      },
>      "usingComponents": {}
>    }
>    ```
>
> 3. 上面的`"custom"`和`"usingComponents"`的声明是必要的。但是如果自定义 tabbar 不生效需要在`index.json`中也做声明：
>
>    ```json
>    {
>      "component": true
>    }
>    ```
>
>    - `index.wxml`
>
>    ```html
>    <view class="tab-bar">
>      <view
>        data-path="/pages/index/index"
>        data-index="0"
>        bindtap="switchTab"
>        class="index"
>      >
>        <view>
>          <image src="{{ selectedPath[0] }}" wx:if="{{ isSelect }}"></image>
>          <image src="{{ path[0] }}" wx:else></image>
>        </view>
>        <view style="color: #1afa29" wx:if="{{ isSelect }}"> 首页 </view>
>        <view style="color: #333333" wx:else> 首页 </view>
>      </view>
>      <view class="center" bindtap="toPark">
>        <view>
>          <image
>            src="/static/icons/park.png"
>            style="width: 80rpx; height: 80rpx"
>          ></image>
>        </view>
>        <view> 停车 </view>
>      </view>
>      <view
>        data-path="/pages/my/my"
>        data-index="1"
>        bindtap="switchTab"
>        class="my"
>      >
>        <view>
>          <image src="{{ path[1] }}" wx:if="{{ isSelect }}"></image>
>          <image src="{{ selectedPath[1] }} " wx:else></image>
>        </view>
>        <view style="color: #333333" wx:if="{{ isSelect }}"> 我的 </view>
>        <view style="color: #1afa29" wx:else> 我的 </view>
>      </view>
>    </view>
>    ```
>
>    - `index.wxss`
>
>    ```css
>    .tab-bar {
>      background-color: #fff;
>      width: 750rpx;
>      height: 170rpx;
>      display: flex;
>      border-top: 1rpx solid #e6e6e6;
>      justify-content: space-around;
>      font-size: 26rpx;
>    }
>    image {
>      width: 64rpx;
>      height: 64rpx;
>    }
>
>    .index {
>      display: flex;
>      flex-direction: column;
>      align-items: center;
>      margin-top: 20rpx;
>    }
>    .my {
>      display: flex;
>      flex-direction: column;
>      align-items: center;
>      margin-top: 20rpx;
>    }
>    .center {
>      position: absolute;
>      top: 5px;
>      display: flex;
>      flex-direction: column;
>      align-items: center;
>    }
>    ```
>
>    - `index.js`
>
>    ```js
>    Component({
>      data: {
>        isSelect: true,
>        selectedPath: [
>          "/static/icons/home_select.png",
>          "/static/icons/my_select.png",
>        ],
>        path: ["/static/icons/home.png", "/static/icons/my.png"],
>        color: "",
>        selectedColor: "",
>      },
>
>      methods: {
>        switchTab(e) {
>          const idx = e.currentTarget.dataset.index;
>          const path = e.currentTarget.dataset.path;
>          console.log(idx);
>          if (idx === "0") {
>            this.setData({
>              isSelect: true,
>            });
>          } else if (idx === "1") {
>            this.setData({
>              isSelect: false,
>            });
>          }
>          console.log(this.data.isSelect);
>          wx.switchTab({
>            url: path,
>          });
>        },
>        toPark() {
>          wx.navigateTo({
>            url: "/mappages/park/park",
>          });
>        },
>      },
>    });
>    ```
>
> 4. 接下来为了使页面切换可以及时设置状态（如颜色改变，页面的及时渲染），我们需要设置每个每个页面的`getTabBar()`方法，uni-app 下在`onShow()`方法中配置：
>
>    ```js
>    const page = this.$mp.page;
>    if (typeof page.getTabBar === "function" && page.getTabBar()) {
>      page.getTabBar().setData({
>        isSelect: true,
>      });
>    }
>    ```

### 注意事项

- 小程序的分包一般将 tabbar 页面作为主包，其他页面作为分包，如果遇到 tabbar 页面所需要的的静态资源过大，则可以考虑将该页面分出，利用中间按钮的功能进入（如抖音中间的加号按钮）;当然也可以切换到一个空页面，再有空页面跳转，但是这样体验不是很好。
- uniapp`tabbar`选项中有`midButton`的设置，但是只在 H5 和 app 端生效，因此小程序需要用到自定义的 tabbar。小程序的原生代码在编译时 uniapp 不会编译改代码段。

​
