---
author: cosin
pubDatetime: 2024-08-02T06:51:58.140Z
title: 2024年被裁员后生活记录02
postSlug: 裁员 生活记录
featured: true
tags:
  - release
description: 被裁之后生活记录
---

# Your next store 项目学习

## typescript关键字 asserts
```ts
export function invariant(condition: unknown, message: string): asserts condition {
	if (!condition) {
		throw new Error(message);
	}
}
```
asserts condition 是一个类型断言，它告诉TypeScript编译器，如果 invariant 函数被调用，并且没有抛出异常，那么 condition 必须为 true。这意味着在 invariant 函数调用之后的代码块中，编译器将 condition 视为非空（non-nullable）的。
```ts
function processUser(input: any) {
  invariant(input, "Input cannot be null or undefined");
  invariant(typeof input.id === "string", "Input must have a string id");
  // 在这里，input被认为是非空的，并且id是一个字符串
  console.log(`Processing user with ID: ${input.id}`);
}

// 使用示例
processUser({ id: "123" }); // 正常执行
processUser(null); // 抛出错误：Input cannot be null or undefined
```
## useDeferredValue
```ts
"use client";

import { useState, useEffect, useDeferredValue } from "react";

export const useDebouncedValue = <T,>(value: T, delay = 300) => {
	const [isPending, setIsPending] = useState(false);
	const [debouncedValue, setDebouncedValue] = useState(value);

	useEffect(() => {
		setIsPending(true);
		const handler = setTimeout(() => {
			setIsPending(false);
			setDebouncedValue(value);
		}, delay);

		return () => {
			setIsPending(false);
			clearTimeout(handler);
		};
	}, [value, delay]);

	return [isPending, useDeferredValue(debouncedValue)] as const;
};
```
useDeferredValue 允许你“延迟”某些值的更新，直到组件的下一次渲染周期。这意味着，即使这些值在当前渲染周期中发生变化，它们也不会立即触发新的渲染，而是会被“推迟”到下一个渲染周期。
#### 跟useState的区别
useState 的状态更新默认是异步的，但更新后会立即反映在组件的渲染中。
useDeferredValue 的值更新是延迟的，直到下一个渲染周期。
useState 适用于需要立即响应状态变化的场景。
useDeferredValue 更适合于处理那些频繁更新但不需要立即反映在UI上的场景，例如动画、滚动同步等。

### Window.matchMedia()
Window 的 matchMedia() 方法返回一个新的 MediaQueryList 对象，表示指定的媒体查询字符串解析后的结果。返回的 MediaQueryList 可被用于判定 Document 是否匹配媒体查询，或者监控一个 document 来判定它匹配了或者停止匹配了此媒体查询。
```ts
export function useMediaQuery(query: string) {
	const [value, setValue] = React.useState(false);

	React.useEffect(() => {
		function onChange(event: MediaQueryListEvent) {
			setValue(event.matches);
		}

		const result = matchMedia(query);
		result.addEventListener("change", onChange);
		setValue(result.matches);

		return () => result.removeEventListener("change", onChange);
	}, [query]);

	return value;
}
```
### React 将HTML封装成组件的正确做法
```ts
const Breadcrumb = React.forwardRef<
	HTMLElement,
	React.ComponentPropsWithoutRef<"nav"> & {
		separator?: React.ReactNode;
	}
>(({ ...props }, ref) => <nav ref={ref} aria-label="breadcrumb" {...props} />);
Breadcrumb.displayName = "Breadcrumb";
```
