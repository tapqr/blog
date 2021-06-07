---
title: Vue-cli 问题汇总
date: 2020-07-23 15:10:51
tags:
 - Vue
 - Vue-cli
---

## Vue-cli 构建工具问题汇总

1. Vue template 模板变量问题

编写vue模板的时候，有些变量不需要双向绑定，会把变量放在export default 对象的外面
比如

```javascript
// TestChild.vue
<template>
  <div>{{text}}</div>
</template>

<script>
let chart = 1;
console.log(chart)
export default {
  props: {
    text: {
      type: String,
      default: 'child'
    }
  }
}
</script>

// parent.vue
<template>
  <div class="about">
    <test-child text="child1"></test-child>
    <test-child text="child2"></test-child>
  </div>
</template>

<script>
import TestChild from '@/components/TestChild'
export default {
  components: { TestChild }
}
</script>

```

编译以后的代码
```javascript
(window["webpackJsonp"] = window["webpackJsonp"] || []).push([["about"],{

/***/ "f820":
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
// ESM COMPAT FLAG
__webpack_require__.r(__webpack_exports__);

// CONCATENATED MODULE: ./node_modules/cache-loader/dist/cjs.js?{"cacheDirectory":"node_modules/.cache/vue-loader","cacheIdentifier":"07d9cba4-vue-loader-template"}!./node_modules/vue-loader/lib/loaders/templateLoader.js??vue-loader-options!./node_modules/cache-loader/dist/cjs.js??ref--0-0!./node_modules/vue-loader/lib??vue-loader-options!./src/views/About.vue?vue&type=template&id=503abc7e&
var render = function () {
  var _vm=this;
  var _h=_vm.$createElement;
  var _c=_vm._self._c||_h;
  return _c('div',{staticClass:"about"},[_c('test-child',{attrs:{"text":"child1"}}),_c('test-child',{attrs:{"text":"child2"}})],1)}
var staticRenderFns = []

// CONCATENATED MODULE: ./src/views/About.vue?vue&type=template&id=503abc7e&

// CONCATENATED MODULE: ./node_modules/cache-loader/dist/cjs.js?{"cacheDirectory":"node_modules/.cache/vue-loader","cacheIdentifier":"07d9cba4-vue-loader-template"}!./node_modules/vue-loader/lib/loaders/templateLoader.js??vue-loader-options!./node_modules/cache-loader/dist/cjs.js??ref--0-0!./node_modules/vue-loader/lib??vue-loader-options!./src/components/TestChild.vue?vue&type=template&id=05867cbc&
var TestChildvue_type_template_id_05867cbc_render = function () {
  var _vm=this;
  var _h=_vm.$createElement;
  var _c=_vm._self._c||_h;
  return _c('div',[_vm._v(_vm._s(_vm.text))])}
var TestChildvue_type_template_id_05867cbc_staticRenderFns = []

// CONCATENATED MODULE: ./src/components/TestChild.vue?vue&type=template&id=05867cbc&

// CONCATENATED MODULE: ./node_modules/cache-loader/dist/cjs.js??ref--12-0!./node_modules/thread-loader/dist/cjs.js!./node_modules/babel-loader/lib!./node_modules/cache-loader/dist/cjs.js??ref--0-0!./node_modules/vue-loader/lib??vue-loader-options!./src/components/TestChild.vue?vue&type=script&lang=js&
//
//
//
//
var chart = 1;
console.log(chart);
/* harmony default export */ var TestChildvue_type_script_lang_js_ = ({
  props: {
    text: {
      type: String,
      default: 'child'
    }
  }
});
// CONCATENATED MODULE: ./src/components/TestChild.vue?vue&type=script&lang=js&
 /* harmony default export */ var components_TestChildvue_type_script_lang_js_ = (TestChildvue_type_script_lang_js_); 
// EXTERNAL MODULE: ./node_modules/vue-loader/lib/runtime/componentNormalizer.js
var componentNormalizer = __webpack_require__("2877");

// CONCATENATED MODULE: ./src/components/TestChild.vue

/* normalize component */

var component = Object(componentNormalizer["a" /* default */])(
  components_TestChildvue_type_script_lang_js_,
  TestChildvue_type_template_id_05867cbc_render,
  TestChildvue_type_template_id_05867cbc_staticRenderFns,
  false,
  null,
  null,
  null
  
)

/* harmony default export */ var TestChild = (component.exports);
// CONCATENATED MODULE: ./node_modules/cache-loader/dist/cjs.js??ref--12-0!./node_modules/thread-loader/dist/cjs.js!./node_modules/babel-loader/lib!./node_modules/cache-loader/dist/cjs.js??ref--0-0!./node_modules/vue-loader/lib??vue-loader-options!./src/views/About.vue?vue&type=script&lang=js&
//
//
//
//
//
//
//

/* harmony default export */ var Aboutvue_type_script_lang_js_ = ({
  components: {
    TestChild: TestChild
  }
});
// CONCATENATED MODULE: ./src/views/About.vue?vue&type=script&lang=js&
 /* harmony default export */ var views_Aboutvue_type_script_lang_js_ = (Aboutvue_type_script_lang_js_); 
// CONCATENATED MODULE: ./src/views/About.vue

/* normalize component */

var About_component = Object(componentNormalizer["a" /* default */])(
  views_Aboutvue_type_script_lang_js_,
  render,
  staticRenderFns,
  false,
  null,
  null,
  null
  
)

/* harmony default export */ var About = __webpack_exports__["default"] = (About_component.exports);

/***/ })

}]);
//# sourceMappingURL=about.38076536.js.map
```
子组件里面额外的局部变量被提取到父组件中，父组件初始化的时候只会执行一次，不会每个子组件中的局部变量都执行一遍。

如果运行以上的代码，只会打印一次chart变量