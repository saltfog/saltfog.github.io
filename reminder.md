---
layout: page
title: Tick-tock. Tick-tock.
description: Self reminder.
keywords: self reminder, manifesto, tick-tock
---

<h4><span id="myClock" style="font-family:'Fira Mono',monospace;letter-spacing:2px;color:#FF4136;text-transform:uppercase"></span></h4>
<script>
var myClock = document.getElementById('myClock');
function renderTime () {
    var currentTime = new Date();
    var months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
    var mo = currentTime.getMonth();
  	var d = currentTime.getDate();
  	var y = currentTime.getFullYear();
    var h = currentTime.getHours();
    var m = currentTime.getMinutes();
    var s = currentTime.getSeconds();
    var ms = currentTime.getMilliseconds();
    if (h < 10) { h = "0" + h; }   
    if (m < 10) { m = "0" + m; }    
    if (s < 10) { s = "0" + s; }
    myClock.textContent = months[mo] + " " + d + ", " + y + " • " + h + ":" + m + ":" + s + ":" + ms;
    myClock.innerText = months[mo] + " " + d + ", " + y + " • " + h + ":" + m + ":" + s + ":" + ms;
}

setInterval(function(){
    renderTime();
}, 1);
</script>

Time is the most important asset. Time does not equal money. Time equals life. And you only have one chance to make it right. Every human being is fighting a battle inside themselves. It’s your obligation to help and inspire them. Regardless of what you do, you can always inspire others to do good. Nobody is better than you. And you’re not better than anybody else. Be humble. Being in the comfort zone is wonderful, but nothing ever grows there. Keep studying. Keep creating. Haters will come if you have the audacity to build something new. Don’t let them define you. Don’t let them stop you. Just block them and keep going. Don’t expect others to make you happy. You are the only one responsible for your happiness. Don’t wait until Friday to enjoy life. Joy should be present in everything you do. Be kind to your parents. They gave up many things for you. Having millions of people admiring you is worthless, if you’re not admired by your own family. Don’t fear the unknown. Fear knowing everything. Life is too damn short and every day counts. Do what you wanna do and do it now. Tick-tock don't stop. Tick-tock don't wait.

<small style="opacity:0.5">Written by Zeno Rocha & Carol Moreschi.</small>
