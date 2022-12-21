本文以`Vue 3`为例：

由于 `textarea`的限制；有时候我们需要实现可定制的富文本编辑器，如：需要文本框随着内容撑高（`textarea`高度是固定的）。

这个时候我们需要利用`div`元素（其他 html 元素也可以）的`contenteditable`属性；

### 创建一个可以自动变换高度的文本域

示例代码：

```vue
<template>
  <div class="my-textarea-component">
    <div class="textarea" contenteditable="true"></div>
  </div>
</template>

<style scoped lang="scss">
.my-textarea-component {
  width: 100%;
  height: 100%;
  min-height: 32px;
  padding-bottom: 8px;
  border-bottom: 2px solid red;
  // 相关样式处理
  .textarea {
    min-height: 20px;
    overflow: hidden;
    font-weight: 500;
    line-height: 20px;
    white-space: break-spaces;
    outline-style: none;
    appearance: none;
  }
}
</style>
```

### 使得文本域的输入受控，并对其进行字数限制

```vue
<template>
  <div class="my-textarea-component">
    <div
      ref="target"
      class="textarea"
      contenteditable="true"
      @input="handleChange"
    ></div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";

const props = defineProps<{
  modelValue?: string;
}>();
const emit = defineEmits(["update:modelValue"]);
const target = ref<HTMLElement | null>(null);

const maxLen = 512;

const handleChange = (e: Event) => {
  // 获取文本
  const text = (e.target as HTMLTextAreaElement).innerText;
  // 进行超出字数处理
  if (text.length > maxLen) {
    (target.value as HTMLTextAreaElement).innerText = text.substring(0, maxLen);
    // 操作其光标到最后
    const curSelection = window.getSelection();
    curSelection?.selectAllChildren(target.value as HTMLTextAreaElement);
    curSelection?.collapseToEnd();
  }
  // 更新值
  emit("update:modelValue", text.substring(0, maxLen));
};
</script>

<style scoped lang="scss">
// 略
</style>
```
