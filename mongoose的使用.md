## mongoose的使用

#### 1. 增删改查API

- 增加数据：`save`

  示例：

  ```js
  const res = {
      customerName,
  	device,
      question,
      auditPeople,
      /** 略 **/
  };
  const tmp = new this.Model(res);
  const result = await tmp.save(); 
  ```

- 删除数据：`remove`、`deleteOne`、`deleteMany`

- 更改数据：`update`、`updateOne`、`updateMany`

- 查询数据：`find`、`findOne`

- 更多API详见[官方文档](https://mongoosejs.com/docs/api/model.html)

#### 2. 默认参数

示例：

```js
const MySchema = mongoose.Schema({
   /** 略 **/
  status: {
    type: Number,
    default: 110, // 未指定status时的默认参数
  },
});
```

#### 3. 预定义模式修饰符(lowercase、uppercase 、trim)

示例：

```javascript
var MySchema=mongoose.Schema({
	name:{
		type:String, 
		trim:true, // mongoose模式修饰符  去除空格 其他两个如字面意思
	}, 
})
```

#### 4. 自定义模式修饰符

​		我们还可以通过 `set`（建议使用） 修饰符在增加数据的 时候对数据进行格式化。也可以通过 `get`（不建议使用）在实例获取数据的时候对数据进行格式化。

示例：

```javascript
var MySchema=mongoose.Schema({
 	redirect:{
		type:String, 
		set(url){
			if(!url) return url;
			if(url.indexOf('http://')!=0 && url.indexOf('https://')!=0){
				url = 'http://' + url;
			}
			return url;
		}
	}, 
})
```

#### 5. 校验参数

> required : 表示这个数据必须传入 
>
> max: 用于 Number 类型数据，最大值 
>
> min: 用于 Number 类型数据，最小值 
>
> enum:枚举类型，要求数据必须满足枚举值 enum: ['0', '1', '2']  ——数组元素需要是 String 类型
>
> match:增加的数据必须符合 match（正则）的规则 
>
> maxlength：String 最大长度
>
> minlength：String 最小长度
>
> ==validate：自定义的验证器，如果通过验证返回 true，没有通过则返回 false==

#### 6. 条件操作符号(用于查询)

- $or——或关系
- $nor——或关系取反
- $gt——大于
- $gte——大于等于
- $lt——小于
- $lte——小于等于
- $ne ——不等于
- $in——在多个值范围内
- $nin——不在多个值范围内
- $all——匹配数组中多个值
- ==$regex——正则，用于模糊查询==
- $size——匹配数组大小
- $maxDistance——范围查询，距离（基于LBS）
- $mod——取模运算
- $near——邻域查询，查询附近的位置（基于LBS）
- $exists——字段是否存在
- ==$elemMatch——匹配内数组内的元素==
- $within——范围查询（基于LBS）
- $box——范围查询，矩形范围（基于LBS）
- $center ——范围醒询，圆形范围（基于LBS）
- $centerSphere——范围查询，球形范围（基于LBS）
- $slice——查询字段集合中的元素（比如从第几个之后，第N到第M个元素）

#### 7. 管道操作符（用于aggregate）

- $project——增加、删除、重命名字段
- $match——条件匹配。只满足条件的文档才能进入下 一阶段 
- ==$limit ——限制结果的数量== 
- ==$skip ——跳过文档的数量（与$limit配合可实现分页）== 
- $sort——条件排序
- $group——条件组合结果 统计
- ==$lookup——操作符 用以引入其它集合的数 据 （表关联查询，也可以使用API:populate）==

#### 8. 更新操作符（用于update相关函数中）

- $inc——将文档中的某个field对应的value自增/减某个数字amount
- $mul——将文档中的某个field对于的value做乘法操作
- $rename——重命名文档中的指定字段的名
- $set——更新文档中的某一个字段，而不是全部替换
- $min——将文档中的某字段与指定值作比较，如果原值小于指定值，则不更新；若大于指定值，则更新
- $max——与$min功能相反
- $currentDate——设置指定字段为当前时间
- $addToSet——用于添加一个元素到array中，一般用于update
- $pop——删除数组中的第一个或最后一个元素，-1表示第一个，没错，第一个；1表示最后一个！
- $pullAll——删除数组或内嵌文档字段中所有指定的元素
- $pull——删除满足条件的元素
- $push往数组中追加指定的元素，若文档中数组不存在，则创建并添加指定元素
