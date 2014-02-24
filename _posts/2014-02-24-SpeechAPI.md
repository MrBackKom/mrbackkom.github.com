---
layout: post
title: "Speech Synthesis API－－让你的Web应用发声说话"
category: JS标准
tags: [web speech API,Speech Synthesis API]
---

　　　[web speech API]("https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html","web speech API")为javascript提供了将语音转换成文字和将文字转换成语音的能力。本篇文章简要介绍一下后者——文字转换成语音，并且对浏览器的版本要求为Chrome 33版本以上的桌面浏览器和移动浏览器。
###基本用法

　　　对于[文字转语音API](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html#tts-section,"synthesis API")最基本的用法就是,调用speechSynthesis.speak()并且发声，

<pre class="">
var msg = new SpeechSynthesisUtterance('Hello World');
window.speechSynthesis.speak(msg);
</pre>

<a href="http://mrbackkomspace.u.qiniudn.com/webspeechAPI.html">try it, What am I reading</a>

　　　不过，你也可以改变参数来实现音量，语速，音调，声音和语言的控制：
<pre class="prettyprint linenums">
var msg = new SpeechSynthesisUtterance();
var voices = window.speechSynthesis.getVoices();
msg.voice = voices[10]; // Note: some voices don't support altering params
msg.voiceURI = 'native';
msg.volume = 1; // 0 to 1
msg.rate = 1; // 0.1 to 10
msg.pitch = 2; //0 to 2
msg.text = 'Hello World';
msg.lang = 'en-US';

msg.onend = function(e) {
  console.log('Finished in ' + event.elapsedTime + ' seconds.');
};

speechSynthesis.speak(msg);
</pre>

###设置语音
　　　API为你提供了接口，获取引擎支持的语音列表：

<pre>
speechSynthesis.getVoices().forEach(function(voice) {
  console.log(voice.name, voice.default ? '(default)' :'');
});
</pre>

然后，通过设置表述的.voice属性来设置不同的语音。

<pre class="prettyprint linenums">
var msg = new SpeechSynthesisUtterance('I see dead people!');
msg.voice = speechSynthesis.getVoices().filter(function(voice) { 
    return voice.name == 'Whisper'; 
})[0];
speechSynthesis.speak(msg);
</pre>


###浏览器兼容性

Chrome 33已经完全支持Web Speech API，不过iOS7上的Safari只提供了部分支持。

###特性检测

既然浏览器有可能支持部分支持Web Speech API（Chromium就是个例子），你也许需要分开检测浏览器是否支持特定的API：

<pre class="prettyprint linenums">
if ('speechSynthesis' in window) {
 // Synthesis support. Make your web apps talk!
}

if ('SpeechRecognition' in window) {
  // Speech recognition support. Talk to your apps!
}
<pre>
