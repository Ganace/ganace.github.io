---
layout: post
title: 【Front】如何一次性渲染十万条数据还能保证页面不卡顿requestAnimationFrame
date: 2024-04-22
categories: Front-end-Foundation
tags: [Front]
author: Ganace
comment: false
---

前端优化方案`如何一次性渲染十万条数据还能保证页面不卡顿`的总结笔记。

---

## requestAnimationFrame 与 fragment（时间分片）结合

`window.requestAnimationFrame()` 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。

`requestAnimationFrame`也是个定时器，不同于`setTimeout`，它的时间不需要我们人为指定，这个时间取决于当前电脑的刷新率，如果是 `60Hz` ，那么就是 `16.7ms` 执行一次，如果是 `120Hz` 那就是 `8.3ms` 执行一次

`fragment`是虚拟文档碎片，我们一次`for`循环产生 20 个`li`的过程中可以全部把真实`dom`挂载到`fragment`上，然后再把`fragment`挂载到真实`dom`上，这样原来需要回流十万次，现在只需要回流 100000 / 20 次。

```js
const total = 100000;
let ul = document.getElementById("container");
let once = 20;
let page = total / once;

function loop(curTotal) {
    if (curTotal <= 0) return;

    let pageCount = Math.min(curTotal, once);

    window.requestAnimationFrame(() => {
        let fragment = document.createDocumentFragment(); // 创建一个虚拟DOM碎片
        for (let i = 0; i < pageCount; i++) {
            let li = document.createElement("li");
            li.innerHTML = ~~(Math.random() * total);
            fragment.appendChild(li); // 挂载
        }
        ul.appendChild(fragment); // 回流
        loop(curTotal - pageCount);
    });
}

loop(total);
```

---
