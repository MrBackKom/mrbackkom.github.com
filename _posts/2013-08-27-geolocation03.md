---
layout: post
title: "HTML5 地理位置教程(3) w3c GeoLocation API [ GeoLocation (HTML5 Geolocation) ]  "
description: ""
category: "HTML5新标准"
tags: [html5,地理位置,geolocation]
---

###一、Geolocation 对象

w3c地理位置API通过Geolocation对象把响应的对象、属性和方法都集成在一起。这个对象可以通过js调用来获取地理位置信息。Geolocation对象挂载在window.navigator对象下，可以通过window.navigator.geolocation,这种方式去访问。可以通过以下这种方式去检测你的浏览器是否支持geolocation对象。

<pre class="prettyprint lang-js linenums">
if (window.navigator.geolocation) {
	// do some geolocation stuff
} else {
	// the browser does not natively support geolocation
}
</pre>

Geolocation对象，包含三个公有方法。
clearWatch(watchId)                                             //通过watchID来停止监听处理
getCurrentPosition(successCallback,[errorCallback, [options]])  //获得当前位置信息的方法，参数为成功回调（必选），和失败回调及其他参数（可选）
watchPosition(successCallback, [errorCallback, [options]])	//在一个特定的时间间隔内去获取位置信息,参数为成功回调（必选），和失败回调及其他参数（可选）.

###二、获取用户位置

一旦我们确定浏览器支持w3c Geolocation API，我们可以通过getCurrentPosition()方法来获取当前位置。这个方法接受至少一个参数，就是成功的回调函数，不过额外也可以传递失败回调和配置参数（PositionOptions）

navigator.geolocation.getCurrentPosition(successCallback, errorCallback,options);这是一个稍微完整点的例子：

<pre class="prettyprint lang-js linenums">
if (window.navigator.geolocation) {
	  navigator.geolocation.getCurrentPosition(successCallback, errorCallback,options);
} else {
	alert('Your browser does not natively support geolocation.');
}
function successCallback(position) {
	// Do something with a location here
}
function errorCallback(error) {
	// There was a problem getting the location
}
</pre>

#三、PositionOptions

navigator.geolocation.getCurrentPosition大家可以注意到在调用这个方法时可以传递第三个参数就是PositionOptions，这个参数同样可以传递到watchPosition()方法中使用。
PositionOptions包含三个属性
enableHighAccurary Boolean   高精度模式是否启用，一旦启用API将会尽可能的去获取最实际、最真实的的位置信息，默认值是false；如果启用这个模式，响应时间会变长，并且电池耗电量会增加。
maximumAge         Integer   指缓存的时间默认是0，例如maximumAge:75000（1分钟是60000）。那么如果10:00整的时候获取过一次地理信息，10:01的时候，再次调用navigator.geolocation.getCurrentPosition，返回的依然是10:00时候的数据。.
timeout            Integer   超时时间，以毫秒为单位。

下面是一个带参数的例子

<pre class="prettyprint lang-js linenums">
var options = {
	enableHighAccuracy: true,
	maximumAge: 60000,
	timeout: 45000
};
</pre>

navigator.geolocation.getCurrentPosition(successCallback, errorCallback,options);
这个调用方式启用了高精度模式，60秒内缓存位置信息，45秒为超时时间


###四、位置缓存

位置缓存是指，应用程序在某一时刻请求位置信息后被记录下来，当请求新的位置信息的时候将返回上次记录的结果。当应用程序不需要频繁的展示位置信息变化的时候，缓存位置是非常好的技术实践能够节省处理的开销。可以通过PositionOptions的maximumAge属性来设置缓存时间。
举个例子

<pre class="prettyprint lang-js linenums">
var options = {
	maximumAge: 600000
};
</pre>

navigator.geolocation.getCurrentPosition(successCallback, errorCallback,options);
上面的这段代码，会缓存1分钟位置信息。如果你不希望缓存位置信息，你就可以不传这个参数，或者把这个参数设成0.如果你想一直读缓存中的数据，将maximumAge值设成Infinity即可

<pre class="prettyprint lang-js linenums">
var options = {
	maximumAge: Infinity
};
navigator.geolocation.getCurrentPosition(successCallback, errorCallback,options);
</pre>


###五、更新用户位置

watchPosition的调用方式和传递参数和getCurrentPosition()相同,两者之间最大的区别就在于当你调用watchPosition方法时，会立马给你一个返回值这个返回值是一个唯一值，但这个方法内部的执行是异步的。
调用方式var watcher = navigator.geolocation.watchPosition(successCallback,errorCallback, options);
再给一个更详细的调用的例子


<pre class="prettyprint lang-js linenums">
var watcher = null;
var options = {
	enableHighAccuracy: true,
	timeout: 45000
};
if (window.navigator.geolocation) {
	watcher = navigator.geolocation.watchPosition(successCallback,errorCallback, options);
} else {
	alert('Your browser does not natively support geolocation.');
}
function successCallback(position) {
	// Do something with a location here
}
function errorCallback(error) {
	// There was a problem getting the location
}
</pre>


###六、无需手动轮训

watchPosition这种模式搭建在一个自动轮训位置更新的策略上，一旦当设备的位置发生变化时，成功回掉会被触发，这样就不需要开发人员自己去定时轮训了。正是由于watchPosition()方法的自动轮训策略，这就给应用程序提供了真正的实时获得地理位置信息的能力。有开发人员自发的轮训其实是一种伪实时操作，而且会降低应用程序的性能。

###七、清除监听

像浏览器的clearTimeout()和clearInterval()，一样，w3c Geolocation API同样可以使用clearWatch()，传入watchID就可以清除监听，语法如下navigator.geolocation.clearWatch(watcher);

下面这个例子是在启动的时候创建位置变化的监听，然后再监听的回掉中清除该监听


<pre class="prettyprint lang-js linenums">
var watcher = null;

var options = {

	enableHighAccuracy: true,

	timeout: 45000

};

if (window.navigator.geolocation) {

	watcher = navigator.geolocation.watchPosition(successCallback,errorCallback, options);

} else {

	alert('Your browser does not natively support geolocation.');

}

function successCallback(position) {

	navigator.geolocation.clearWatch(watcher);

	// Do something with a location here

}
</pre>



###八、成功回调

当调用getcurrentPositon（）和updatePositon（）方法时，如果成功操作，则成功操作的回调函数会被触发，并且会携带一个position对象作为参数。

1.Position对象，自身携带两个属性

coords 坐标对象，包含地理坐标及其他属性值。

timestamp 一个dom时间戳，包好位置对象被创建的时间。

2.Coordinates 对象

Position对象中coords就是Coordinates对象的实例，Coordinates对象包含以下属性

latitude 			纬度，十进制数.

longitude 			经度，十进制数.

altitude 			海拔.

accuracy 			经纬度的准确度，单位是米.

altitudeAccuracy 	高度的准确性，如果不支持则为null。

heading 			设备行进的方向，安顺时针方向从0到360度，当设备部移动的情况下该值为NaN，如果不支持反悔null

speed     			该设备的当前对地速度，测量单位为米每秒。如果不支持，这个值是空的。


<pre class="prettyprint lang-js linenums">
var options = {

		enableHighAccuracy: true,

		maximumAge: 1000,

		timeout: 45000

	};

	if (window.navigator.geolocation) {

		navigator.geolocation.getCurrentPosition(successCallback,errorCallback, options);

	} else {

		alert('Your browser does not natively support geolocation.');

	}

	function successCallback(position) {

		var output = '';

		output += "Your position has been located.\n\n";

		output += 'Latitude: ' + position.coords.latitude + "°\n";

		output += 'Longitude: ' + position.coords.longitude + "°\n";

		output += 'Accuracy: ' + position.coords.accuracy + " meters\n";

		if (position.coords.altitude)

			output += 'Altitude: ' + position.coords.altitude + " meters\n";

		if (position.coords.altitudeAccuracy)

			output += 'Altitude Accuracy: ' + position.coords.altitudeAccuracy +" meters\n";

		if (position.coords.heading)

			output += 'Heading: ' + position.coords.Heading + "°\n";

		if (position.coords.speed)

			output += 'Speed: ' + position.coords.Speed + " m/s\n";

			output += 'Time of Position: ' + position.timestamp;

			alert(output);'

	}

	function errorCallback(error) {

		// There was a problem getting the location

	}
</pre>



###九、失败回调函数

##1.PositionError 对象

当调用getCurrentPosition和uodatePosition失败时，注册的失败的回掉函数会被触发，病携带会一个PositionError 对象的实例。

对象通常包含两个属性
code 	错误码:

		PERMISSION_DENIED (1)

			权限错误

		POSITION_UNAVAILALE (2)

			设备不可以通过定位

		TIMEOUT (2)

			超时

message  错误信息


看个例子


<pre class="prettyprint lang-js linenums">
var options = {

	enableHighAccuracy: true,

	maximumAge: 1000,

	timeout: 45000

};

if (window.navigator.geolocation) {

	navigator.geolocation.getCurrentPosition(successCallback,errorCallback, options);

} else {

	alert('Your browser does not natively support geolocation.');

}

function successCallback(position) {

	var output = '';

	output += "Your position has been located.\n\n";

	output += 'Latitude: ' + position.coords.latitude + "°\n";

	output += 'Longitude: ' + position.coords.longitude + "°\n";

	output += 'Accuracy: ' + position.coords.accuracy + " meters\n";

if (position.coords.altitude)

	output += 'Altitude: ' + position.coords.altitude + " meters\n";


if (position.coords.altitudeAccuracy)

	output += 'Altitude Accuracy: ' + position.coords.altitudeAccuracy +" meters\n";

if (position.coords.heading)

	output += 'Heading: ' + position.coords.Heading + "°\n";

if (position.coords.speed)

	output += 'Speed: ' + position.coords.Speed + " m/s\n";

output += 'Time of Position: ' + position.timestamp;

alert(output);

}

function errorCallback(error) {

	switch (error.code) {

		case error.PERMISSION_DENIED:

			alert('You have denied access to your position.');

			break;

		case error.POSITION_UNAVAILABLE:

			alert('There was a problem getting your position.');

			break;

		case error.TIMEOUT:

			alert('The application has timed out attempting to get your ' +location.');

			break;

	}

}

</pre>

###十、隐私问题

隐私是一个十分重要的问题，不管是w3c Geolocation API 还是对应用程序来说，隐私保密都是重中之重。公司十分关心隐私问题，所以才收集用户一些相对数据的同时要做很多保护措施。如果用户开始怀疑某家公司窥探他们的隐私了，这家公司必须要调整策略，否则只有思路一条。

出于对位置信息的保密和隐私保护策略，w3c Geolocation API，在调用的时候，会先弹出黄条提示，让用去决定你的应用程序是否可以定位用户的位置，来劲可能的以用户为主导。只有用户在允许的情况下，你才可以使用定位功能。