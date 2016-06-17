---
layout:     keynote
title:      "Transitioning from Server to Client Side Web Development with Angular 2"
subtitle:   "Keynote: JavaScript Modularization Journey"
iframe:     
date:       2016-06-17
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - Angular
		- 前端
---
#Angular 1.x 编码规则
##Angular 团队支持
<非常感谢Angular团队领导Igor Minar,检查审视，贡献必要的资料

##目录
	1.[单一职责](#single-responsibility)
	1.[IIFE](#iife)
	1.[Modules](#modules)
	1.[Controllers](#controllers)
	1.[Services](#services)
	1.[Factories](#factories)
	1.[数据服务](#dataservices)
	1.[Directives](#directives)
	1.[Promises解决方案](#promises)
	1.[手动依赖注入](#manualdi)
	1.[压缩和注解](#minification-and-annotation)
	1.[异常处理](#exception-handling)
	1.[命名规则](#naming)
	1.[应用结构优化准则](#application-structure-lift-principle)
	1.[应用结构](#application-structure)
	1.[模块化](#modularity)
	1.[启动](#startup-logic)
	1.[Angular $服务包装](#angular-wrapper-services)
	1.[测试](#testing)
	1.[动画](#animations)
	1.[评论](#comments)
	1.[JSHint](#js-hint)
	1.[JSCS](#jscs)
	1.[常量](#constants)
	1.[文件模板和代码片段](#file-templates-and-snippets)
	1.[路由](#routing)
	1.[自动化任务](#task-automation)
	1.[Filter过滤器](#filter)
	1.[Angular 文档](#angular-docs)

## 单一职责

### 规则1
##### [Style [Y001](#style-y001)]

	- 每个文件仅包含一个组件，并且使其少于400行代码，它的好处在于
		1.对于测试来说更加容易
		1.使得代码可读性更强，更好维护，避免团队在源代码控制方面的问题
		1.可以避免 多个组件在共享变量，创建不必要的闭包，或者不必要的耦合和依赖 时隐藏的错误

```javascript
/*不规范示例*/
angular
	.module('app',['ngRoute'])
	.controller('SomController',SomeController)
	.factory('SomeFactory',SomeFactory);

	function SomeController(){}

	function SomeFactory(){}
```

```javascript
/*推荐*/
//app.module.js
angular
	.module('app',['ngRoute']);
```
```javascript
/*some.controller.js*/
angular
	.module('app')
	.controller('SomeController',SomeController);

	function SomeController() { }
```
```javascript
/* recommended */

// some.factory.js
angular
		.module('app')
		.factory('someFactory', someFactory);

function someFactory() { }
```
**[返回顶部](table-of-contents)**

## IIFE (立即执行方法 Immediately Invoked Function Expression(IIFE)).
### Javascript Scopes
###### [Style [Y010](#style-y010)]
	- 将Angular组件包含在IIFE代码块中，这样的优势在于
	1.一个IIFE将变量从全局作用域中移出，这会有效的方式变量和方法之间的命名冲突
	1.当你将你的代码从开发板 压缩和打包 到生产服务器上的时候，会有很多全局作用域下的变量发生冲突，
	这时IIFE就可以有效的隔离这些变量

	```javascript
  /* 不规范 */
  // logger.js
  angular
      .module('app')
      .factory('logger', logger);

  // logger 方法已经添加到了全局作用域中
  function logger() { }

  // storage.js
  angular
      .module('app')
      .factory('storage', storage);

  // storage 方法已经添加到了全局作用域中
  function storage() { }
  ```

  ```javascript
  /**
   * 推荐
   *
   * 下面代码中没有全局作用域变量和方法
   */

  // logger.js
  (function() {
      'use strict';

      angular
          .module('app')
          .factory('logger', logger);

      function logger() { }
  })();

  // storage.js
  (function() {
      'use strict';

      angular
          .module('app')
          .factory('storage', storage);

      function storage() { }
  })();
  ```
	**[返回顶部](#table-of-contents)**
## Modules

### 避免命名冲突
###### [Style [Y020](#style-y020)]
	- 使用统一的子模块命名规则可以帮助避免模块命名冲突。分隔符帮助定义模块和他们的子模块之间的层次结构
	例如`app`可以作为父模块名，`app.dashboard`和`app.users`模块作为依赖于`app`模块的子模块。

### 定义(or 设置)
###### [Style [Y021](#style-y021)]

	- 可以使用模块的setter语法定义模块，而不要使用变量定义模块。
			在单一组件文件中，使用到模块变量的情况会变得非常少。
			```javascript
		  /* 避免使用 */
		  var app = angular.module('app', [
		      'ngAnimate',
		      'ngRoute',
		      'app.shared',
		      'app.dashboard'
		  ]);
		  ```

		  ```javascript
		  /* 推荐方式 */
		  angular
		      .module('app', [
		          'ngAnimate',
		          'ngRoute',
		          'app.shared',
		          'app.dashboard'
		      ]);
		  ```

### Getters
###### [Style [Y022](#style-y022)]
	- 当使用一个模块的时候，可以使用getter链而非变量装载。
		这样可以使项目代码的可读性更高并且避免变量冲突

		```javascript
	  /* 避免使用 */
	  var app = angular.module('app');
	  app.controller('SomeController', SomeController);

	  function SomeController() { }
	  ```

	  ```javascript
	  /* 推荐使用 */
	  angular
	      .module('app')
	      .controller('SomeController', SomeController);

	  function SomeController() { }
	  ```
### Getter 方法和Setter 方法之间的比较
###### [Style [Y023](#style-y023)]

```javascript
/* recommended */

// 创建一个模块
angular.module('app', []);

// 从模块集合中取出这个模块
angular.module('app');
```

### 命名 与 匿名方法之间的比较
###### [Style [Y024](#style-y024)]

	- 在回调函数中尽量使用命名方法代替匿名方法，这样会使得代码的可读性更高，并且提升代码可复用的程度
	```javascript
  /* 避免使用 */
  angular
      .module('app')
      .controller('DashboardController', function() { })
      .factory('logger', function() { });
  ```

  ```javascript
  /* 推荐使用 */

  // dashboard.js
  angular
      .module('app')
      .controller('DashboardController', DashboardController);

  function DashboardController() { }
  ```

  ```javascript
  // logger.js
  angular
      .module('app')
      .factory('logger', logger);

  function logger() { }
  ```
**[Back to top](#table-of-contents)**

## 控制器Controllers

### controllerAs View Syntax
###### [Style [Y030](#style-y030)]
	-使用[`controllerAs`](http://www.johnpapa.net/do-you-like-your-angular-controllers-with-or-without-sugar/) 语句替代`经典控制器$scope`语句
		1.控制器构造、新建的时候回提供一个简单的实例，而`controllerAs`语句相比`传统$scope 语句`更接近Javascript原声方法
		1.它会在视图中使用一个虚拟的对象绑定（例如 使用`customer.name` 代替 `name`）,更加贴近上下文关系，
		增加刻度性，避免引用冲突问题
		1.帮助避免在视图嵌套控制器中使用`$parent`

		```html
	  <!-- avoid -->
	  <div ng-controller="CustomerController">
	      {{ name }}
	  </div>
	  ```

	  ```html
	  <!-- recommended -->
	  <div ng-controller="CustomerController as customer">
	      {{ customer.name }}
	  </div>
	  ```

### controllerAs 控制器语句
###### [Style [Y031](#style-y031)]

	- 使用`controllerAs`语句替代`传统controller中的$scope`

	- `controllerAs`语句在Controller内部使用`this`将数据绑定在`$scope`上
	`controllerAs` 是`$scope`语法糖。你依然可以使用`$scope`方法.

	*Why?*:`controllerAs`语句是`$scope`基础上的一种语法糖，你可以继续使用`$scope`绑定变量和方法。

	*Why?*:帮助避免在控制器中使用`$scope`方法，用其他的方式实现或者将他们移动到工厂方法中，并在控制器中引用.控制器应仅在需要的时候使用`$scope`，再实例中，当我们使用[`$emit`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$emit), [`$broadcast`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$broadcast) 或者 [`$on`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$on). 一个变量或者声明一个事件

	```javascript
/* 避免使用 */
function CustomerController($scope){
	$scope.name = {};
	$scope.sendMessage = function(){};
}

/* 推荐 */
function CustomerController(){
	this.name = {};
	this.sendMessage = function {};
}
	```

### controllerAs 和 VM
###### [Style [Y032](#style-y032)]

	- 当使用`controllerAs`语法的时候，捕捉一个变量替代`this`，选择统一的变量名`vm`，代表 ViewModel。

	*Why?*：`this` 关键字是作为上下文对象，当使用控制器内部的方法有可能会改变它的作用域，捕捉上下文中的`this`避免遇到这种问题

	```javascript
	/* 错误的示例 */
	function CustomerController(){
			this.name = {};
			this.sendMessage = {};
	}

	/* 推荐的方式 */
	function CustomerController(){
			var vm = this;
			vm.name = {};
			vm.sendMessage = function(){}
	}
	```

注意:你通过在代码上添加注释避免任何的[jshint](http://jshint.com/)警告。但是，这不需要将方法使用大写命名，这只是个对于构造方法的惯例。

```javascript
/* jshint validthis : true */
var vm = this;
```

注意:当在控制器中使用`controller as`创建一个监听时，你可以监听`vm.*`
(创建监听的时候需要小心监听导致的更多的负载)

```html
<input ng-model = "vm.tite"/>
```

```javascript
function SomeController($scope,$log){
		var vm = this;
		vm.title = 'Some Title';

		$scope.watch('vm.title',function(){
				$log.info('vm.title was %s',original);
		})
}
```

注意:当基于一个庞大的代码库工作时，使用更多的描述性命名可以帮助简化的认知负荷和搜素，避免坑长的名字和繁琐的输入。

```html
<!-- 避免使用 -->
<input ng-model="customerProductItemVm.title">
```

```html
<!-- 推荐 -->
<input ng-model="productVm.title">

```

### 将绑定成员提取到首位

	- 将需要绑定的成员按照字母顺序放在控制器的上方，并且不要扩散到控制器代码

	*Why?*:将需要绑定的成员防止在首位可以提升代码的可读性，并且帮你快速识别这个控制器需要绑定的成员都有哪些

	*Why?*:设置匿名行内函数当然可以使得代码简单，但是当哪些方法体多余一行代码的时候，他们会很大程序上降低代码可读性。将方法定义在绑定成员的下方（哪些方法会被提升）将实现细节移动到下方，保持绑定成员在上方，使代码可读性更好。

```javascript
/* 避免 */
function SessionsController(){
	var vm = this;

	vm.gotoSession = function(){

	};

	vm.refresh = function(){

	};
}

```

```javascript
/* 推荐 */
function SessionsController(){
	var vm = this;

	vm.gotoSession = gotoSession;
	vm.refresh = refresh;
	vm.search = search;

	function gotoSession(){}

	function refresh(){}

	function search(){}
}

```

    ![Controller Using "Above the Fold"](https://raw.githubusercontent.com/johnpapa/angular-styleguide/master/a1/assets/above-the-fold-1.png)

注意：如果这个方法的代码只有一行可以将它保持在上方，这样并不影响可读性。

```javascript
/* 避免 */
function SessionController(data){
	var vm = this;

	vm.gotoSession = gotoSession;

	vm.refresh = function(){
		/**
		 * lines
		 * of
		 * code
		 * affects
		 * readabibity
		*/
	}
	vm.search = search;
	vm.sessions = [];
	vm.title = 'Sessions';
}
```

```javascript
/* 推荐方式 */
function SessionsController(sessionDataService) {
		var vm = this;
		vm.gotoSession = gotoSession;
		vm.refresh = sessionDataService.refresh; // 1 liner is OK
		vm.search = search;
		vm.sessions = [];
		vm.title = 'Sessions';
}
```

### 通过方法定义隐藏实现
###### [Style [Y034](#style-y034)]

	- 使用方法定义隐藏方法实现细节。保持你的邦定成员在上方，当你需要在一个控制器中绑定一个方法的时候，它指向出现在这个文件中的一个函数声明。这是直接绑定部分成员，更多细节详见[这篇文章](http://www.johnpapa.net/angular-function-declarations-function-expressions-and-readable-code/)

```javascript
/* 避免使用 */
function AvengersController(avengersService,logger){
		var vm = this;
		vm.avengers = [];
		vm.title = 'Avengers';

		var activate = function(){
				return getAvengers().then(function(){
						logger.info('Activated Avengers View');
				})
		}

		var getAvengers = function(){
				return avengersService.getAvengers().then(function(data){
						vm.avengers = data;
						return vm.avengers;
				});
		}

		vm.getAvengers = getAvengers;

		activate();

}

注意:在上面的示例中重要的东西都很分散。下面的代码中，注意重要的东西都在上方

```javascript
/* 推荐
使用方法定义，绑定成员定义在上方
*/

function AvengersController(avengerService,logger){
		var vm = this;
		vm.avengers = [];
		vm.getAvengers = getAvengers;
		vm.title = '';

		active();

		function activate(){
				return getAvengers().then(function(){
						logger.info('Activated Avengers View');
				})
		}

		function geetAvengers(){
				return avengersService.getAvengers().then(function(data){
						vm.avengers = data;
						return vm.avengers;
				});
		}
}
```

### 将控制器移动到服务(Service)中
###### [Style [Y035](#style-y035)]

	- 将控制器中的逻辑移动到服务器或者工厂方法中

```javascript

/* avoid */
function OrderController($http, $q, config, userInfo) {
	 var vm = this;
	 vm.checkCredit = checkCredit;
	 vm.isCreditOk;
	 vm.total = 0;

	 function checkCredit() {
			 var settings = {};
			 // Get the credit service base URL from config
			 // Set credit service required headers
			 // Prepare URL query string or data object with request data
			 // Add user-identifying info so service gets the right credit limit for this user.
			 // Use JSONP for this browser if it doesn't support CORS
			 return $http.get(settings)
					 .then(function(data) {
						// Unpack JSON data in the response object
							// to find maxRemainingAmount
							vm.isCreditOk = vm.total <= maxRemainingAmount
					 })
					 .catch(function(error) {
							// Interpret error
							// Cope w/ timeout? retry? try alternate service?
							// Re-reject with appropriate error for a user to see
					 });
	 };
}
```

```javascript
/* 推荐 */
function OrderController(creditService){
		var vm = this;
		vm.checkCredit = checkCredit;
		vm.isCreditOk;
		vm.total = 0;

		function checkCredit(){
				return creditService.isOrderTotalOk(vm.total)
					.then(function(isOk){ vm.isCreditOk = ok; })
					.catch(showError);
		}
}
```
### 使控制器更专注
###### [Style [Y037](#style-y037)]

	- 为一个视图定义一个控制器，不要试图在其他视图中复用控制器，而是将可以复用的逻辑转移到工厂中，保持控制器的简单并专注与当前视图。

	*Why?*: 在很多的视图上复用控制器是脆弱的，端对端（end-to-end）测试需要保证访问一个复杂的应用时的稳定性

### 分配控制器
###### [Style [Y038](#style-y038)]

	- 当一个控制器必须搭配一个视图并且其他组件需要复用其他的控制器或视图，在它的路由中单独定义控制器。

	注意: 如果视图加载依赖其他视图，这意味着另外的一种途径，尝试使用`ng-controller="Avengers as vm"` 语句.
