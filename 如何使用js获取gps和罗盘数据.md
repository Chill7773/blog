## 如何使用js获取gps和罗盘数据

### 定位

```js

// 获取当前经纬度位置
export const getLocation = async () => {
  return new Promise((resolve, reject) => {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(
        function (res) {
          resolve({
            lng: res.coords.longitude,
            lat: res.coords.latitude,
          });
        },
        function () {
          resolve({});
        },
        {
          enableHighAccuracy: true, 
          timeout: 5000, 
          maximumAge: 0, 
          provider: "system",
          coordsType: "wgs84",
          geocode: false,
        }
      );
    } else {
      resolve({});
    }
  });
};
```

> ######`navigator.geolocation.getCurrentPosition`参数详解
>
> -  `enableHighAccuracy` ：是否获取高精度经纬度，默认值为false，如果想要==获取高精度数据==，此项需开启
> - ` timeout`：获取位置信息的超时时间。单位为毫秒（ms），默认值为不超时
> - `maximumAge`：获取位置信息的缓存时间。单位为毫秒（ms），默认值为0（立即更新获取）。如果设备缓存的位置信息超过指定的缓存时间，将重新更新位置信息后再返回。
> -  `provider`：(String 类型 )优先使用的定位模块。可取以下供应者： =="system"==：表示系统定位模块，支持wgs84坐标系； =="baidu"==：表示百度定位模块，支持gcj02/bd09/bd09ll坐标系； =="amap"==：表示高德定位模板，支持gcj02坐标系。 默认值按以下优先顺序获取（amap>baidu>system），若指定的provider不存在或无效则返回错误回调。注意：百度/高德定位模块需要配置百度/高德地图相关参数才能正常使用。平台支持Android - 2.2+ (支持)，iOS - 4.5+ (支持): provider为“baidu”时，仅支持bd09ll坐标系，暂不支持高德定位模块。
> -  `coordsType`：(String 类型 )指定获取的定位数据坐标系类型。可取以下坐标系类型： ==“wgs84”==：表示WGS-84坐标系；==“gcj02”==：表示国测局经纬度坐标系； ==“bd09”==：表示百度墨卡托坐标系； ==“bd09ll”==：表示百度经纬度坐标系；provider为“system”时，默认使用“wgs84”类型；“baidu”是，默认使用“bd09ll”类型。 如果设置的坐标系类型provider不支持，则返回错误。
> -   `geocode`：(Boolean 类型 )是否解析地址信息。解析的地址信息保存到Position对象的address、addresses属性中，true表示解析地址信息，false表示不解析地址信息，返回的Position对象的address、addresses属性值为undefined，默认值为true。如果解析地址信息失败则返回的Position对象的address、addresses属性值为null。



### 方向

```js
 if (window.DeviceOrientationEvent) {
    // 用于旋转
 	window.addEventListener("deviceorientation", eventRotateMark);
 }

// ios下罗盘事件触发调用的api
const eventRotateMark = (event) => {
  // const angle = event.alpha - 180 >= 0 ? event.alpha - 180 : event.alpha + 180;
  const angle = event.webkitCompassHeading
    ? 360 - event.webkitCompassHeading
    : Math.round(360 - event.alpha);
  let now = Date.now();
  if (now - lastExecution < 200) return; //用于限制多长时间调用该方法
  lastExecution = now;
  
};


//ios下需要先获取权限
export const getPermission = () => {
  if (
    typeof window.DeviceMotionEvent !== "undefined" &&
    typeof window.DeviceMotionEvent.requestPermission === "function"
  ) {
    window.DeviceMotionEvent.requestPermission()
      .then(function (state) {
        if ("granted" === state) {
          //用户同意授权
        } else {
          //用户拒绝授权
          alert(
            "该应用的导航功能需要授权设备运动权限,请重启微信后,再次进行授权!"
          );
        }
      })
      .catch(function (err) {
        alert("error: " + err);
      });
  }
};

```

- 注意：安卓下无法获取正北方向，因为i安卓下没有`webkitCompassHeading`参数

###使用full-tilt库获取设备的罗盘数据

- 该库的使用地址在[github上](https://github.com/adtile/Full-Tilt)，建议使用`script`标签引入，源码地址在[此地址](https://github.com/adtile/Full-Tilt/blob/master/dist/fulltilt.min.js)

- 使用方法如下：

  ```js
  let fulltilt = window.FULLTILT // 接取变量
  const promise = new fulltilt.getDeviceOrientation({ 'type': 'world' }); //实例化，type为world表示获取罗盘数据
  promise
  	.then(function (deviceOrientation) {
         if (deviceOrientation) {
          	deviceOrientation.listen(function () {
         	 		var currentOrientation = deviceOrientation.getScreenAdjustedEuler();
   	      		var angle = currentOrientation.alpha; 
          	});·
         } else {
            alert("没有指南针功能")
         }
  	})
      .catch(function (message) {
  		console.error(message);
          alert("指南针功能未开启")
  	});
  ```

- 该工具库可以在ios与安卓上使用，但是安卓系统由于自身指南针存在误差，所以并不准确，需要做校准后使用

