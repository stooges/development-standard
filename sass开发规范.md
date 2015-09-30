# sass开发规范

> + CSS 扩展
> 	- 嵌套选择器
> 	- 父选择器：&
> + SCSS 脚本
> 	- 变量：$
> 	- 插值：#{}
> + @ 指令
> 	- @import 
> 	- @extend
> + Mixin（混入指令）
> 	- 定义 mixin：@mixin
> 	- 使用 mixin：@include

这些功能能满足几乎所有需求。

#### 1. 嵌套选择器

以前的

	#main { color: #000; }
	#main p { color: #00ff00; width: 97%; }
	#main p .redbox { background-color: #ff0000; color: #000000; }

用 SCSS 为

	#main { color: #000;
		p { color: #00ff00; width: 97%;
			.redbox { background-color: #ff0000; color: #000000; }
		}
	}

#### 2. 父选择器：&

	.link { color: #DDD; }
	.link:hover { color: red; }

用 SCSS 为

	.link { color: #DDD; 
		&:hover { color: red; }
	}

#### 3. 变量：$

这次不对照 CSS 相信你也能看懂：

	$box-width: 100px;
	
	.box { width: $box-width; }
	.little-box { width: $box-width / 2; }

没错，上面用到了除法，这也是 CSS 所不具备的。

#### 4. @import

SCSS 中的 @import 和 CSS 的差不多，会引入一个文件。

	@import "foo"; 
	@import "foo.scss"; /* 没有后缀时会默认为 scss 文件，所以这两行等价。 */
	@import "foo.css";
	@import "http://foo.com/bar.css";
	@import url(foo);

不过 SCSS 更高级的地方是，你可以在任何地方引入一个文件，而不仅仅是文件开头。

	#main {
		@import "example.scss";
	}

曾经我们经常被教导：不要使用 @import。因为 CSS 里的 @import 会发起网络请求，而且效果又不能令人满意。但是在 SCSS 里就不存在这个问题了，因为 SCSS 可以在本地把导入的 SCSS 文件合并成单独一个 CSS 文件。

#### 5. @extend

SCSS 中用 @extend 指令来实现选择器间的继承：

	.error { border: 1px #f00; background-color: #fdd; }
	.seriousError { 
		@extend .error; 
		border-width: 3px; 
	}

编译为的 CSS 为

	.error, .seriousError { border: 1px #f00; background-color: #fdd; }
	.seriousError { border-width: 3px; }

可以把 @extend 理解为：凡是出现 .error 选择器的地方都会出现 .seriousError 选择器，如此一来，.error 拥有的所有属性，.seriousError 也同样拥有。

#### 6. @mixin 和 插值：#{}

回到之前说的一段 CSS 代码

	.box{
		-webkit-border-radius: 5px;
		-moz-border-radius: 5px;
		-ms-border-radius: 5px;
		-o-border-radius: 5px;
		border-radius: 5px;
	}

我们可以使用 @mixin 把它变得「可复用」，而不用每次都敲那么多前缀

	$prefixes : -webkit-, -moz-, -ms-, -o-,"";
	
	@mixin border-radius ($radius) {
    	@each $pre in $prefixes {
        	#{$pre}border-radius:$radius;
    	}
	}

下次要写 border-radius 时只需一句即可

	.box {
		@include border-radius (5px);
		@include border-radius (10px);
	}

如果再深入挖掘，我希望在写 box-shadow、transition、transform 等 CSS 3 属性时能不能也复用一个方法呢？请看下面

	$prefixes : -webkit-, -moz-, -ms-, -o-,"";

	@mixin prefix($name, $argument) {
		@each $pre in $prefixes {
        	#{$pre}#{$name}:$argument;
    	}
	}

好了，现在所有前缀都不需要自己加了

	div {
		@include prefix(border-radius, 5px );
		@include prefix(transform, rotate(7deg) );
		@include prefix(transition, width 2s );
	}
