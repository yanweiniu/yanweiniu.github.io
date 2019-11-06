---
title: Sails总结
tags: []
categories: []
date: 2018-07-07 10:46:22
---
[ 指南文章和博文](https://sailsjs.com/faq "常见问题（指南、文章和博文）")
[开发文档](https://sailsjs.com/documentation/reference "开发文档")
---- 
## 简介
Sails 主要是使用 Express 为核心，再整合其他常用的 Express Middleware 及的模组，以约定大于配置为发展导向，我们只要使用 Sails.js 框架，即可达到简单并快速的建构一个具有 MVC 稳定基础的网站架构，随之利用这个架构扩充它，便可以打照一个企业级的架构。
<!-- more -->
> Sails1.0以上版本和以下版本差异较大，学习时网上资源多为低版本，建议多看官方API，虽然是英文的。

> sails中controller默认不支持URL路由访问了，直接访问会出现404错误，需要在blueprints中开启action支持（action：true），除了默认路由，Sails也允许使用config/routes.js文件让你手动绑定路由到控制器的动作。

`model.find() // 查询所有 ` 

## 目录结构
- myApp
	- api
		1.  controllers(控制器相关，业务逻辑)
			2. models(模块开发)
			3. policies(用于路由过滤)
			4. responses(定制所需的响应:如404 res.notFound())
			5. services(定制一些常用的工具类–全局的)
		- assets(静态资源文件)
			1. images
			2. js
			3. styles
			4. templates
			5. avicon.ico
		- config(整个项目的配置文件系统)
			1. env(配置不同环境的变量)
			2. *.js
		- tasks
		- views
## controllers篇
---- 
它的责任是接收request 并回response 给client 端，常作为models 和views 的中间层。
> 惯例上，新增一个Controller 须使用驼峰命名，且第一个字母是大写。

	module.exports={
	  hello: async(req,res)=> {
	  res.send('hello man!!!');
	  }
	  }
	此时访问localhost:1337/user/hello,此时你会发现访问成功并返回数据hello man!!!;
	在UserController下面我们定义了一个hello 的动作(action),那么sails会自动生成action routers(可以禁用该路由访问方式，详见下面的config篇)，生成规则：domain/:user(不需要加controller)/:actionName
	
*注意：默认的action routers 只能是get请求方式，如果需要其他请求方式需要在配置文件中配置*

### 渲染模板
	如何渲染页面，sails提供了自己渲染方式，新建sails项目默认使用ejs作为模板。当然sails支持常用的一些模板，如：ejs、jade、hbs、swig等(详见sails官网)。渲染方式：
				  module.exports={
					  hello:function(req,res){
						  res.view({data:{name:'test123'}})							//该方式会默认去找views/user/hello.ejs文件
						  //res.view('hello',{data:{name:'test123'}})			  	//指定页面views/hello.ejs
					  }
			  		}
### Service
**为什么要有Service**
因为Controller 负责所有流程的控制，所以我们希望Controller 一直保持在很简洁的状态，所以我们会尽量把复杂的计算逻辑及商业逻辑都搬到Service ，以利重复使用跟管理。

**逻辑计算**
	getServiceFee : async(user, order) => {
	  let isVip = UserService.isVip(user);
	  if(order.paymentTotalAmount > largeOrderAmount || isVip ){
	return 0; // 免運
	  }else{
	return 70;
	  }
	}
**复杂对象生成**
	getNewUser : async(...略...) => {
	  let newUser = {
	    username: newUser.username,
	    email: newUser.email,
	    // (略...)
	    password: newUser.password
	  }
	  return newUser;
	}
### 创建Service
文件目录：/api/service
	module.exports = {
	  isAdmin: function(user) {
	let isAdmin = false;
	if (user) {
	  user.Roles.forEach((role) => {
	if(role.authority == 'admin') isAdmin = true;
	  });
	}
	return isAdmin;
	  }
	}
**Service 如何使用**
为了方便使用Sails.js 框架上的models，service 会自动定义为全域变量(/config/globals.js)。
	// api/controllers/AdminController
	module.exports = {
	isSuperPermission: function (req, res) {
	const loginUser = AuthService.getLoginUser(req);
	const isAdmin = AuthService.isAdmin(loginUser);
	res.ok({isAdmin: isAdmin});
	}
	}

## policies
定义权限检查策略
	module.exports = function(req, res, next) {
	  // 檢查使用者是否已經登入，若登入則執行下一個處理程序。
	  if (req.session.authenticated) {
	return next();
	  }
	
	  // 若使用者沒有登入，則回傳 403。
	  return res.forbidden('You are not permitted to perform this action.');
	};
## model篇
model,即数据模型，定义数据结构，一般定义好后需要定义相应的controller，定义如下：

	module.exports={
	attributes:{
	username:{
	type:'string',
	required:true
	},
	password:{
	type:'string',
	required:true
	}
	}
	}
### attributes
定义模型的属性：属性类型
string、text、integer、float、date、datetime、boolean、binary、array、json、email
collection:关联其他model数组
model：关联其他model
如下定义特殊的属性
	attributes:{
	uuid:{
	type:'string',
	size: 24		 //定义属性的大小，必须保证适配器支持，如mysql
	primaryKey: true,	//设置主键，每个模型只有一个主键，尽量在autoPK设置为false才使用
	required:true
	},
	state:{
	type:'string',
	enum: ['pending', 'approved', 'denied']	/保证该属性的值必须为该数组中的值
	required:true
	},
	
	username:{
	type:'string',
	required:true,
	columnName: 'full_name'	//当我们需要兼容其他表的结构，而又需要自己设计合理的字段的时候
	//可以使用该属性指定表中的字段名，而在sails中还是使用username字段
	},
	password:{
	type:'string',
	defaultsTo: '123456'	//没有初始化的默认值
	minLength: 6,	//设置值最小长度
	required:true	//是不是必须的
	},
	count:{
	type:'integer',
	autoIncrement: true//当我们没有指定值时，属性自动递增，一般用于integer的类型
	unique：true//是否唯一,如果设置为true保证数据库中的数据该属性具有唯一性
	required:true						
	},
	knownDialects:{
	collection:'Dialect'
	},
	 spouse: { model: 'Person' }
	
	
}
### Model和ORM
Model 就是MVC 中的M，与Database schema 有密切的关系，甚至是完全可以对应。
在Sails.js的理想下，它希望将所有的数据库都一视同仁，不管你是使用mongodb或着是MySQL都使用同样的语法呼叫数据库，可以无痛切换，所以定义了操作数据库的ORM模块Waterline，然后每一个不同的数据库，有独立的adapter跟waterline衔接。
但理想是梦幻的现实是残酷的，要将原本就差异很大的关联式数据库与NOSQL 数据库，视为一视同仁，势必得舍去许多东西，ORM也非常难定义，再加上每个adapter得需跟上数据库改版的脚步，可以想像到的是一场灾难，这也是Sails.js 架构中，一直很诟病的地方，所以我并不会在waterline 中多琢磨，下篇直接介绍替代方案。

### 生命周期回调
1、创建时的回调
> beforeValidate: fn(values, cb)
> afterValidate: fn(values, cb)
> beforeCreate: fn(values, cb)
> afterCreate: fn(newlyInsertedRecord, cb)
2、修改的时候回调
> beforeValidate: fn(valuesToUpdate, cb)
> afterValidate: fn(valuesToUpdate, cb)
> beforeUpdate: fn(valuesToUpdate, cb)
> afterUpdate: fn(updatedRecord, cb)
3、销毁的时候回调
	beforeDestroy: fn(criteria, cb)
	afterDestroy: fn(destroyedRecords, cb)
	如在创建用户的时候对密码机密操作：
	var bcrypt = require('bcrypt');
	
	module.exports = {
	attributes: {
	username: {
	type: 'string',
	required: true
	},
	password: {
	type: 'string',
	minLength: 6,
	required: true,
	columnName: 'encrypted_password'
	}
	},
	beforeCreate: function (values, cb) {
	bcrypt.hash(values.password, 10, function(err, hash) {
	if(err) return cb(err);
	values.password = hash;
	cb();
	});
	}
	};
### 建立APi
`sails generate api article`
产生两个档案分别是api/models/Article.js 和api/controllers/ArticleController.js
### 建立model
`sails generate model article title:string, content:text`
延伸阅读：[attributes](https://sailsjs.com/documentation/concepts/models-and-orm/attributes "Attributes")
### 建立controller
在/api/controller下，建立一个名字为ArticleController.js的档案，里面的method包含有create、update、destory。
`sails generate controller article create update destroy`
### 手动路由
	// config/routes.js
	module.exports.routes = {
	  'get /signup': { view: 'conversion/signup' },
	  'post /signup': 'AuthController.processSignup',
	  'get /login': { view: 'portal/login' },
	  'post /login': 'AuthController.processLogin',
	  '/logout': 'AuthController.logout',
	  'get /me': 'UserController.profile'
	}
### 自动路由
路由基本上要做的就是url 跟controller 的连接，在一开始我们就一直强调，Sails.js 是一个约定大于配置的框架，所以当然它也有不需配置就能使用的路由设计机制。
通过config/blueprints.js的设置开启，可以做到不需手动设定，就能是识别你controller 的action 要走哪个router。
	GET /user -> UserController.find
	GET /user/:id -> UserController.findOne
	POST /user -> UserController.create
	PUT /user/:id -> UserController.update
	DELETE /user/:id -> UserController.destroy
### 方法
1、模型已经自带有某些方法：.create(), .update(), .destroy(), .find(), etc.
	create():创建一个记录
	
	User.create({username:'jy',password:'123456'}).exec(function (err,created){
	console.log(created);			//返回的是创建的对象
	   })
	
	findOne({username:'jy'}):				//返回第一个对象
	find({username:'jy'}):				  	//返回一个数组
	count({username:'jy'}):				 	//返回结果为一个该结果集的条数
	destroy({name:'Flynn'})：				//销毁找到的结果
	findOrCreate({username:'jy'},{username:'jyjy'})：//查询是否有第一个参数的记录，没有就创建第二个参数的记录
	update({username:'jy'},{username:'jyjy'})：	//修改记录
	
	User.update({username:'jy'},{username:'Flynn'}).exec(function (err,updated){
	
	if (err) {
	// handle error here- e.g. res.serverError(err);
	console.log(err)
	}else{
	console.log(updated)
	}
	});
	
	
	以上方法都是使用的exec来执行生产结果(返回query对象)||也可以直接传回调参数
	
	query：	用于直接调用底层的数据库驱动程序,如：直接使用原生的sql语句,适用于mysql或者相关的数据库
	User.query('select * from user',function(err,results){
	console.log(results);
	})
	native:只适用于使用mongodb的情况,返回mongodb的对象直接操作数据库
	stream：						一般用于socket的操作
	var getSocket = req.socket;
	User.stream({name:'Walter'}).pipe(getSocket.emit);
	2、我们也可以自定义方法：定义好后我们可以在任何动作中通过moduleName.methodName()调用
	module.exports={
	attributes:{
	username:{
	type:'string',
	required:true
	},
	password:{
	type:'string',
	required:true
	}
	},
	findOnePerson:function(str){
	console.log('findOnePerson---'+str)
	}
	}
3、sails动态生成的方法
如：User模型中有一个属性为username，User会为你生成一个findByUsername('someone').exec(function(err,user){})

