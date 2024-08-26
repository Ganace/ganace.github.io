---
layout: post
title: 【Front】在表单校验场景中， 如何实现页面视口滚动到报错的位置(scrollIntoView api)
date: 2024-04-24
categories: Front-end-Foundation
tags: [Front]
author: Ganace
comment: false
---

前端中级热点题: 在表单校验场景中， 如何实现页面视口滚动到报错的位置(scrollIntoView api)

---

## Element.scrollIntoView()

`Element` 接口的 `scrollIntoView()` 方法会滚动元素的父容器，使被调用 `scrollIntoView()` 的元素对用户可见。

```js
scrollIntoView();
scrollIntoView(alignToTop);
scrollIntoView(scrollIntoViewOptions);
```

#### alignToTop 可选

一个布尔值：

-   如果为 true，元素的顶端将和其所在滚动区的可视区域的顶端对齐。相应的 scrollIntoViewOptions: {block: "start", inline: "nearest"}。这是这个参数的默认值。
-   如果为 false，元素的底端将和其所在滚动区的可视区域的底端对齐。相应的 scrollIntoViewOptions: {block: "end", inline: "nearest"}。

#### scrollIntoViewOptions 可选

一个包含下列属性的对象：

###### behavior 可选

定义滚动是立即的还是平滑的动画。该选项是一个字符串，必须采用以下值之一：

-   smooth：滚动应该是平滑的动画。
-   instant：滚动应该通过一次跳跃立刻发生。
-   auto：滚动行为由 scroll-behavior 的计算值决定。
-   block 可选
    定义垂直方向的对齐，start、center、end 或 nearest 之一。默认为 start。

-   inline 可选
    定义水平方向的对齐，start、center、end 或 nearest 之一。默认为 nearest。

## Vue 项目中的 scrollIntoView 应用

```js
// utils.js 封装的scrollToError方法
/**
 * 自动滚动到错误的校验框
 *
 * @param {*} el 包裹的元素
 * @param {string} [scrollOption={
 *     behavior: 'smooth',
 *     block: 'center'
 *   }] 滚动参数
 */
const scrollToError = (
    el,
    scrollOption = {
        behavior: "smooth",
        block: "center",
    }
) => {
    const isError = el.getElementsByClassName("is-error");
    isError[0].scrollIntoView(scrollOption);
};

// 应用到Vue组件中
<el-form ref="componentFormEle">
...
</el-form>

validate() {
  return new Promise(resolve => {
    this.$refs.componentFormEle.validate(async valid => {
      if (!valid) {
        await this.$nextTick()
        this.$utils.scrollToError(this.$refs.componentFormEle.$el)
      }
      resolve(valid)
    })
  })
}
```

---
