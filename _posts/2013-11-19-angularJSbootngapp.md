---
layout: post
title: "angularJS启动多个ng-app"
description: ""
category: angularJS
tags: ["angularJS"]
---

ng-app是angulajs模板引擎当中的一个十分重要的标签，从“app"这个字眼就不难看出其含义，在我理解为一个模块或者页面中的一个应用模型，在一个使用angularjs框架的页面中，很可能使用多个模块，原因是不同模块的业务逻辑不同，代码在理论上就不应该耦合在一起这样不利于维护。下面的几行代码展示了一种angularjs启动多个模块的demo.

<pre class="prettyprint lang-js linenums">
&lt;!DOCTYPE html&gt;
&lt;html &gt;
&lt;head&gt;
    &lt;title&gt;&lt;/title&gt;
    &lt;script src="http://code.jquery.com/jquery-2.0.3.min.js"&gt;&lt;/script&gt;
&lt;/head&gt;
&lt;body &gt;
    &lt;div id="Lilei"  ng-controller="Textcontroller"&gt;
        &lt;p&gt;{{content.message}}&lt;/p&gt;
    &lt;/div&gt;
    &lt;div id="Hanmeimei" ng-controller="Textcontroller"&gt;
        &lt;p&gt;{{content.message}}&lt;/p&gt;
    &lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>


<pre class="prettyprint lang-js linenums">
&lt;script src="http://code.angularjs.org/1.2.0/angular.min.js"&gt;&lt;/script&gt;
&lt;script&gt;
    var myapp1mod = angular.module('Lilei',[]);
    myapp1mod.controller('Textcontroller',function($scope){
        var content= {};
        content.message = "Hello Lilei";
        $scope.content= content;
    });

    var myapp2mod = angular.module('Hanmeimei',[]);
    myapp2mod.controller('Textcontroller',function($scope){
        var content= {};
        content.message = "Hello Hanmeimei";
        $scope.content= content;
    });

    angular.bootstrap(angular.element("#Lilei"),["Lilei"]);
    angular.bootstrap(angular.element("#Hanmeimei"),["Hanmeimei"]);

&lt;/script&gt;
</pre>


angular.bootstrap(angular.element("#Lilei"),["Lilei"]);

angular.bootstrap(angular.element("#Hanmeimei"),["Hanmeimei"]);

