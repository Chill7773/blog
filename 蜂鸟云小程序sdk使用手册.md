## 蜂鸟云SDK使用(微信小程序版本，以uni-app框架为例)

### 使用蜂鸟云SDK的任务场景

> 当客户需要使用某些场景的自定义地图*(例如停车场，商场，一幢建筑等)*时，我们可以使用蜂鸟云sdk去解决这个问题。
>
> 我们可以根据需求利用[蜂鸟云在线绘图工具](https://cloud.fengmap.com/)绘制一个符合客户要求的地图，然后使用sdk调用这个地图并进行操作。

### 使用蜂鸟云SDK所能达到的目标

> 比如我们利用蜂鸟云在线绘图工具绘制了如下的一个基本地图
>
> ![](https://ftp.bmp.ovh/imgs/2021/05/72c64e77c13e6eff.png)
>
> 在微信小程序中调用这个地图，效果如下：
>
> ![](https://ftp.bmp.ovh/imgs/2021/05/b95f7d329f0c5547.png)
>
> 我们可以对这款地图做拖动、缩放、路径绘制、3D变换等些许操作

###蜂鸟云SDK的简单使用

##### 初始化准备

- 进入[蜂鸟云官网](https://developer.fengmap.com/develop-wxapp.html)，下载微信小程序sdk
- 将名称为`fengmap.miniprogram.min.js`放入项目中，引入方式为`import {fengmap} from '../../libs/fengmap.miniprogram.min.js'`

##### 加载地图

1. 首先在`<template>`中创建两个`canvas`画布，==type类型不能填错==

   ```html
   <canvas type="webgl" id="fengmap" style="width: 100%; height: 100%;" disable-scroll @touchstart="touchStart" @touchmove="touchMove" @touchend="touchEnd"></canvas>
   
   <canvas type="2d" id="overlay"></canvas>
   ```

2. 在data中定义一个`canvas:null`，然后再`onReady(){}`生命周期函数中键入如下代码：

   ```js
   uni.createSelectorQuery().select('#fengmap').node().exec((res) => {
   	const canvas = res[0].node;
   	this.canvas = canvas
   	uni.createSelectorQuery().select("#overlay").node().exec((tempRes) => {
   		const tmpCanvas = tempRes[0].node;
   		// 	后面所有代码都在这里编写		
   	})
   })
   ```

3. 实例化地图类，并挂载到canvas画布上，注意下面的`fmap`实例==**需要使用const或let定义**，**不能在data中定义，使用this调用**==，否则会出现报错（uniapp中出现，小程序原生SDK不会出现）。

   ```js
   const mapOptions = {
   	canvas: canvas, 			// 必要，webgl画布
   	tempCanvas: tmpCanvas, 		// 必要，2d画布
   	appName: 'shareparking',	// 必填
   	key: '76f53e0b68954d43eb4c3a06bdcf8fa8',	//必填
   	defaultViewMode: fengmap.FMViewMode.MODE_2D,
   	defaultMapScale: 10000,
   	mapScaleRange: [1000, 10000],
   	defaultControlsPose: 90,
   	defaultThemeName: '1001'
   };
   const fmap = new fengmap.FMMap(mapOptions);
   fmap.openMapById(fmapID, function(error) {// fmapIDk是自己绘制的地图的ID号
   	//打印错误信息
   	// console.log(error);
   });
   fmap.on('loadComplete', () => {
   	console.log('地图加载完成');			
   })
   ```

##### 地图拖拽、缩放动作添加

1. 在`type`为`webgl`的canvas标签下添加` disable-scroll @touchstart="touchStart" @touchmove="touchMove" @touchend="touchEnd"`这些属性

2. 在`methods`中定义相关函数

   ```js
   // 手指触摸动作开始
   touchStart(e) {
       this.canvas.dispatchTouchEvent({
           ...e,
           type: 'touchstart'
       })
   },
   // 手指触摸后移动
   touchMove(e) {
       this.canvas.dispatchTouchEvent({
           ...e,
           type: 'touchmove'
       })
   },
   // 手指触摸动作结束
   touchEnd(e) {
       this.canvas.dispatchTouchEvent({
        	...e,
           type: 'touchend'
       })
   }
   ```

##### 路径绘制

1. 在`methods`中定义一个`createNavi`的绘图函数

   ```javascript
   createNavi(fmap,coords){
       this.navi = new fengmap.FMNavigation({
           //地图对象
           map: fmap,
           //导航结果文字描述内容的语言类型参数, 目前支持中英文。参考FMLanguaeType。
           naviLanguage: fengmap.FMLanguageType.ZH,
           //导航中路径规划模式, 支持最短路径、最优路径两种。默认为MODULE_SHORTEST, 最短路径。
           naviMode: fengmap.FMNaviMode.MODULE_SHORTEST,
           //导航中的路线规划梯类优先级, 默认为PRIORITY_DEFAULT, 详情参考FMNaviPriority。
           naviPriority: fengmap.FMNaviPriority.PRIORITY_DEFAULT,
           //调用drawNaviLine绘制导航线时, 是否清除上次调用drawNaviLine绘制的导航线, 默认为true
           autoClearNaviLine: true,
           //导航线与楼层之间的高度偏移设置。默认是1。
           lineMarkerHeight: 1.5,
           // 设置导航线的样式
           lineStyle: {
               // 导航线样式
               lineType: fengmap.FMLineType.FMARROW,
               // 设置线的宽度
               lineWidth: 6,
               //设置线动画,false为动画
               noAnimate: true
           }
       });
       console.log(123)
       console.log(this.navi)
       this.navi.setStartPoint({
           x: coords[0].x,
           y: coords[0].y,
           groupID: coords[0].groupID,
           // url: '../../images/start.png',
           size: 32
       });
       // console.log(navi.setStartPoint)
   
       //添加终点
       this.navi.setEndPoint({
           x:  coords[1].x,
           y:  coords[1].y,
           groupID: coords[1].groupID,
           // url: '../../images/end.png',
           size: 32
       });
       // 画出导航线
       this.navi.drawNaviLine();
   },
   ```

2. 定义一个`coords`数组，其中`x`和`y`为经纬度，`groupID`为楼层

   ```js
   const coords = [{
       x: 12956877.927671516,
       y: 4853666.259914725,
       groupID: 1
   },
   {
       x: 12956859.77386272,
       y: 4854301.642712345,
       groupID: 1
   }];
   ```

3. 在`fmap.on`的函数中执行`createNavi`

   ```js
   fmap.on('loadComplete', () => {
   	console.log('地图加载完成');
       //地图加载完执行画导航路径
       this.createNavi(fmap,coords)
    })
   ```

#### 定位标记点

##### 获取用户当前位置

* 首先我们需要获取用户的当前经纬度的信息。我们可以通过`uniapp`的原生接口来进行获取用户当前的经纬度数据。但是这种方法获取的的数据和真实数据相差较大，不能做到实时的定位。这时我们就要考虑第三方的逆地址解析。
* 我对比了高德的微信小程序SDK和腾讯的SDK，发现在小程序SDK上腾讯地图的SDK更为准确一点。关于腾讯SDK的操作[详细参照](https://lbs.qq.com/miniProgram/jsSdk/jsSdkGuide/methodReverseGeocoder)。

##### 将经纬度转换为蜂鸟云坐标

```js
//坐标转换
transfer(x, y) {//x为经度 y为纬度
const [K, PI] = [20037508.34, Math.PI]
return [x * K / 180, (Math.log(Math.tan((90 + y) * PI / 360)) / (PI / 180)) * K / 180]
}
```

##### 向地图添加mark点标记

```js
//添加坐标
addmarker(data, fmap) {
this.locationMarker = new fengmap.FMLocationMarker(fmap, {
		//x坐标值
		x: data[0],
		//y坐标值
		y: data[1],
		//图片地址
		url: "/static/images/location.png",
		//楼层id
		groupID: 1,
		//图片尺寸
		size: 48,
		//marker标注高度
		height: 3
	})
	fmap.addLocationMarker(this.locationMarker);
	this.marked = 1
	return this.locationMarker
},
```

- 这里需要注意的是url选项，必须要是绝对路径且需要建立一个`static`文件夹(关于这点我也不晓得原因，反正就是静态资源要放在static文件夹下，否则就会报错)。

##### 旋转mark坐标

- 我们可以注意到上面的函数返回了一个变量，这个是坐标点的实例。我们拿到这个实例后可以对坐标点进行相关操作。

```js
// 在罗盘监控方法里调用旋转坐标方法
uni.onCompassChange(function(res) {
	that.rotatemark(res.direction,lm)
});

rotatemark(angle,lm){
	lm.rotateTo({
        to: angle,
		duration: 0.5
	});
}
```

##### 移动mark坐标

```js
movemark(data, lm) {
	console.log('data', data)
	console.log('lm', lm)
	lm.moveTo({
		x: data[0],
		y: data[1],
		groupID: 1
	});
},
```





#### 注意事项

- 在进行sdk绘制路径的时候，如果使用的是自定义地图，需要先在地图上进行路径的绘制
- `new fengmap.FMMap`的时候一定要`const`一个变量去接，使用`this`会出现`three.js`的渲染报错
- 地图创建完成后，一切对于地图的操作例如导航路径、标记添加等都要在`fmap.on('***',()=>{})`的回调函数中
- mapOptions中的`defaultThemeName`选项是对地图的主题设置，具体主题样式[可以查询](https://developer.fengmap.com/resource-theme.html)，也可以进行自定义的主题编辑。
- 相关参数都可以在官方sdk文档中查询，具体操作可以下载官方demo研究

### 总结

> 如果是简单的自定义地图，蜂鸟云SDK是不错的选择。但是由于蜂鸟云的在线地图绘制工具感觉并不是特别好用，而且功能有限，对于绘制精确且精美的地图难度较大(不过可以联系官方定制)。
>
> 目前可以做到地图显示、拖拽缩放地图、2D\3D变换、路径绘制,坐标点的绘制，坐标点的旋转移动。但是目前由于经纬度获取，原生SDK获取的不准确，第三方SDK获取响应时间过长（大概需要10S左右）。所以关于实时定位功能还在研究中。
>
> ==文档持续更新中......==