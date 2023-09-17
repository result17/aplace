---
author: cosin
pubDatetime: 2023-09-17T10:02:45+08:00 
title: vue challenger 答案
featured: false
draft: false
tags:
  - color-schemes
  - docs
description:
  vue challenger 答案记录
---

# Prop验证
```ts
// 你的答案
<script setup lang="ts">
interface ButtonProps {
  type: 'primary' | 'ghost' | 'dashed' | 'link' | 'text' | 'default'
};

withDefaults(defineProps<ButtonProps >(), {
  type: 'default'
})
</script>

<template>
  <button>Button</button>
</template>
```