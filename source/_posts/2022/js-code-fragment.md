---
title: js-code-fragment
date: 2022-09-15 15:08:32
cover: http://q.265265.xyz/imgs%2Fblog%2F1_4iXLdAjcfF7jn4QT_wjbhQ_a.png
top_img: http://q.265265.xyz/imgs%2Fblog%2F1_4iXLdAjcfF7jn4QT_wjbhQ.png
tags: 
 - js
---

+ 禁止浏览器缩放
```js
const keyCodeMap = {
    // 91: true, // command
    61: true,
    107: true, // 数字键盘 +
    109: true, // 数字键盘 -
    173: true, // 火狐 - 号
    187: true, // +
    189: true, // -
};
// 覆盖ctrl||command + ‘+’/‘-’
document.onkeydown = function (event) {
    const e = event || window.event;
    const ctrlKey = e.ctrlKey || e.metaKey;
    if (ctrlKey && keyCodeMap[e.keyCode]) {
        e.preventDefault();
    } else if (e.detail) { // Firefox
        event.returnValue = false;
    }
};
// 覆盖鼠标滑动
document.addEventListener('wheel', (e) => {
    if (e.ctrlKey) {
        if (e.deltaY < 0) {
            e.preventDefault();
            return false;
        }
        if (e.deltaY > 0) {
            e.preventDefault();
            return false;
        }
    }
}, { passive: false });
```