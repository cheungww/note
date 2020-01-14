## 简介

- 之前我们都是通过shell来完成对数据库的各种操作的，在开发中大部分时候我们都需要通过程序来完成对数据库的操作。
- 而Mongoose就是一个让我们可以通过Node来操作MongoDB的模块。
- Mongoose是一个对象文档模型（ODM）库，它对Node原生的MongoDB模块进行了进一步的优化封装，
  并提供了更多的功能。
- 在大多数情况下，它被用来把结构化的模式应用到一个MongoDB集合，并提供了验证和类型转换等好处



**mongoose的好处**

- 可以为文档创建一个模式结构（Schema）
- 可以对模型中的对象/文档进行验证
- 数据可以通过类型转换转换为对象模型
- 可以使用中间件来应用业务逻辑挂钩
- 比Node原生的MongoDB驱动更容易



## 新的对象

mongoose中为我们提供了几个新的对象

- Schema(模式对象)：Schema对象定义约束了数据库中的文档结构
- Model：Model对象作为集合中的所有文档的表示，相当于MongoDB数据库中的集合collection
- Document：Document表示集合中的具体文档，相当于集合中的一个具体的文档



## 通过mongoose连接MongoDB

1. 下载安装Mongoose

   ```shell
   npm i mongoose --save
   ```

2. 在项目中引入mongoose

      ```js
   var mongoose = require("mongoose");
   ```

3. 连接MongoDB数据库

   ```js
   mongoose.connect('mongodb://数据库的ip地址:端口号/数据库名', { useMongoClient: true});
   ```

   如果端口号是默认端口号（27017） 则可以省略不写

4. 断开数据库连接(一般不需要调用)
   MongoDB数据库，一般情况下，只需要连接一次，连接一次以后，除非项目停止服务器关闭，否则连接一般不会断开

   ```js
   mongoose.disconnect()
   ```



## connection

- 一旦连接了MongoDB数据库，底层的Connection对象就可以通过mongoose模块的conection属性来访问。
- connection对象是对数据库连接的抽象，它提供了对象连接、底层的Db对象和表示结合的Model对象的访问。
- 并且可以对connection对象上的一些事件进行监听，来获悉数据库连接的开始与端开。
- 比如，可以通过open和close事件来监控连接的打开和关闭。

```js
// 数据库连接成功的事件
mongoose.connection.once("open",function(){});

// 数据库断开的事件
mongoose.connection.once("close",function(){});
```



## Schema模式对象

- 使用Mongoose你必须经常定义模式。
- 模式为集合中的文档定义字段和字段类型。
- 如果你的数据是被结构化成支持模式的，这是非常有用的。
- 简单来说，模式就是对文档的约束，有了模式，文档中的字段必须符合模式的规定。否则将不能正常操作。

### 定义模式

- 模式为集合中的文档定义字段和字段类型。
- 对于在模式中的每个字段，你都需要定一个特定的值类型。受支持的类型如下：
  - String
  - Number
  - Boolean
  - Array
  - Buffer
  - Date
  - ObjectId或Oid
  - Mixed
- 需要为你计划使用的每个不同的文档类型都定义一个模式。

### 创建模式定义

模式需要通过mongoose的Schema属性来创建，这个属性是一个构造函数。

```js
new Schema(definition,option)
```

- definition（描述模式）
- options 配置对象，定义与数据库中集合的交互

```js
// 创建schema
let userSchema = new mongoose.Schema({
  username: {
    type: String,
    index: 1,
    unique: true,
    require: true
  },
  password: String,
  hobby: [String],
  age: Number
});
```

**options常用选项**
- autoIndex

  布尔值，开启自动索引，默认true

- bufferCommands

  布尔值，缓存由于连接问题无法执行的语句，默认true

- capped

  集合中最大文档数量

- collection

  指定应用Schema的集合名称

- id

  布尔值，是否有应用于_id的id处理器，默认true

- _id

  布尔值，是否自动分配id字段，默认true

- strict

  布尔值，不符合Schema的对象不会被插入进数据库，默认true



## Model模型对象

- 一旦定义好了Schema对象，就需要通过该Schema对象来创建Model对象。
- 一旦创建好了Model对象，就会自动和数据库中对应的集合建立连接，以确保在应用更改时，集合已经创建并具有适当的索引，且设置了必须性和唯一性。
- Model对象就相当于数据库中的集合，通过Model可以完成对集合的CRUD操作。

- 创建模型对象需要使用mongoose的model()方法，语法如下：

  ```js
  model(name, [schema], [collection] , [skipInit])
  ```

  - name参数相当于模型的名字，以后可以同过name找到模型。

  - schema是创建好的模式对象。

  - collection是要连接的集合名。

  - skipInit是否跳过初始化，默认是false。

  - 一旦把一个Schema对象编译成一个Model对象，你就完全准备好开始在模型中访问、添加、删除、更新和删除文档了。也就是说有了模型以后我们就可以操作数据库了。

示例

```js
var mongoose = require("mongoose");
mongoose.connect("mongodb://127.0.0.1/mongoose_test",{useMongoClient:true});
mongoose.connection.once("open",function () {
	console.log("数据库连接成功~~~");
});


//将mongoose.Schema 赋值给一个变量
var Schema = mongoose.Schema;

//创建Schema（模式）对象
var stuSchema = new Schema({

	name:String,
	age:Number,
	gender:{
		type:String,
		default:"female"
	},
	address:String

});

//通过Schema来创建Model
//Model代表的是数据库中的集合，通过Model才能对数据库进行操作
//mongoose.model(modelName, schema):
//modelName 就是要映射的集合名 mongoose会自动将集合名变成复数
var StuModel = mongoose.model("student" , stuSchema);

//向数据库中插入一个文档
//StuModel.create(doc, function(err){});
StuModel.create({
	name:"白骨精",
	age:16,
	address:"白骨洞"
},function (err) {
	if(!err){
		console.log("插入成功~~~");
	}
});
```



### Model对象的方法

- remove(conditions, callback)

- deleteOne(conditions, callback)

- deleteMany(conditions, callback)

- find(conditions, projection, options, callback)

- findById(id, projection, options, callback)

- findOne(conditions, projection, options, callback)

- count(conditions, callback)

- create(doc, callback)

- update(conditions, doc, options, callback)

- 等等

示例

```js
var mongoose = require("mongoose");
mongoose.connect("mongodb://127.0.0.1/mongoose_test",{useMongoClient:true});
mongoose.connection.once("open",function () {
	console.log("数据库连接成功~~~");
});

var Schema = mongoose.Schema;

var stuSchema = new Schema({

	name:String,
	age:Number,
	gender:{
		type:String,
		default:"female"
	},
	address:String

});

var StuModel = mongoose.model("student" , stuSchema);
/*
	- 有了Model，我们就可以来对数据库进行增删改查的操作了

 	Model.create(doc(s), [callback])
 	- 用来创建一个或多个文档并添加到数据库中
 	- 参数：
 		doc(s) 可以是一个文档对象，也可以是一个文档对象的数组
 		callback 当操作完成以后调用的回调函数

 	查询的：
	 Model.find(conditions, [projection], [options], [callback])
	 	- 查询所有符合条件的文档 总会返回一个数组
	 Model.findById(id, [projection], [options], [callback])
	 	- 根据文档的id属性查询文档
	 Model.findOne([conditions], [projection], [options], [callback])
	 	- 查询符合条件的第一个文档 总和返回一个具体的文档对象

 		conditions 查询的条件
 		projection 投影 需要获取到的字段
 			- 两种方式
 				{name:1,_id:0}
 				"name -_id" 负号表示不显示，所以此处只显示name字段，不显示_id字段
 		options  查询选项（skip limit）
 				{skip:3 , limit:1}
 		callback 回调函数，查询结果会通过回调函数返回
 					回调函数必须传，如果不传回调函数，压根不会查询

 */

/*StuModel.find({name:"唐僧"},function (err , docs) {
	if(!err){
		console.log(docs);
	}
});*/

/*StuModel.find({},{name:1 , _id:0},function (err , docs) {
	if(!err){
		console.log(docs);
	}
});*/

/*StuModel.find({},"name age -_id", {skip:3 , limit:1} , function (err , docs) {
	if(!err){
		console.log(docs);
	}
});*/

/*StuModel.findOne({} , function (err , doc) {
	if(!err){
		console.log(doc);
	}
});*/

/*StuModel.findById("59c4c3cf4e5483191467d392" , function (err , doc) {
	if(!err){
		//console.log(doc);
		//通过find()查询的结果，返回的对象，就是Document，文档对象
		//Document对象是Model的实例
		console.log(doc instanceof StuModel);
	}
});*/



/*StuModel.create([
	{
		name:"沙和尚",
		age:38,
		gender:"male",
		address:"流沙河"
	}

],function (err) {
	if(!err){
		console.log(arguments);
	}
});*/


/*
	修改
 Model.update(conditions, doc, [options], [callback])
 Model.updateMany(conditions, doc, [options], [callback])
 Model.updateOne(conditions, doc, [options], [callback])
 	- 用来修改一个或多个文档
 	- 参数：
 		conditions 查询条件
 		doc 修改后的对象
 		options 配置参数
 		callback 回调函数
 Model.replaceOne(conditions, doc, [options], [callback])
* */

//修改唐僧的年龄为20
/*StuModel.updateOne({name:"唐僧"},{$set:{age:20}},function (err) {
	if(!err){
		console.log("修改成功");
	}
});*/

/*
	删除：
 Model.remove(conditions, [callback])
 Model.deleteOne(conditions, [callback])
 Model.deleteMany(conditions, [callback])
 */
/*StuModel.remove({name:"白骨精"},function (err) {
	if(!err){
		console.log("删除成功~~");
	}
});*/

/*
 Model.count(conditions, [callback])
 	- 统计文档的数量的
 */
StuModel.count({},function (err , count) {
	if(!err){
		console.log(count);
	}
});
```



## Document文档对象

- 通过Model对数据库进行查询时，会返回Document对象或Document对象数组。

- Document继承自Model，代表一个集合中的文档。

- Document对象也可以和数据库进行交互操作。

  

**Document对象的方法**

- equals(doc)

- id

- get(path,[type])

- set(path,value,[type])

- update(update,[options],[callback])

- save([callback])

- remove([callback])

- isNew

- isInit(path)

- toJSON()

- toObject()

示例

```js
var mongoose = require("mongoose");
mongoose.connect("mongodb://127.0.0.1/mongoose_test",{useMongoClient:true});
mongoose.connection.once("open",function () {
	console.log("数据库连接成功~~~");
});

var Schema = mongoose.Schema;

var stuSchema = new Schema({

	name:String,
	age:Number,
	gender:{
		type:String,
		default:"female"
	},
	address:String

});

var StuModel = mongoose.model("student" , stuSchema);
/*
	Document 和 集合中的文档一一对应 ， Document是Model的实例
		通过Model查询到结果都是Document
 */

//创建一个Document
var stu = new StuModel({
	name:"奔波霸",
	age:48,
	gender:"male",
	address:"碧波潭"
});

/*
	document的方法
 		Model#save([options], [fn])
 */
/*
stu.save(function (err) {
	if(!err){
		console.log("保存成功~~~");
	}
});*/

StuModel.findOne({},function (err , doc) {
	if(!err){
		/*
		 	update(update,[options],[callback])
		 		- 修改对象
		 	remove([callback])
		 		- 删除对象

		 */
		//console.log(doc);
		/*doc.update({$set:{age:28}},function (err) {
			if(!err){
				console.log("修改成功~~~");
			}
		});*/

		/*doc.age = 18;
		doc.save();*/

		/*doc.remove(function (err) {
			if(!err){
				console.log("大师兄再见~~~");
			}
		});*/


		/*
			get(name)
				- 获取文档中的指定属性值
			set(name , value)
				- 设置文档的指定的属性值
			id
				- 获取文档的_id属性值
			 toJSON() ******
			 	- 转换为一个JSON对象

			 toObject()
			 	- 将Document对象转换为一个普通的JS对象
			 		转换为普通的js对象以后，注意所有的Document对象的方法或属性都不能使用了

		 */
		//console.log(doc.get("age"));
		//console.log(doc.age);

		//doc.set("name","猪小小");
		//doc.name = "hahaha";

		//console.log(doc._id);
		//var j = doc.toJSON();
		//console.log(j);

		//var o = doc.toObject();

		//console.log(o);

		doc = doc.toObject();

		delete doc.address;

		console.log(doc._id);

	}
});
```








