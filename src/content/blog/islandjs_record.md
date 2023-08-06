---
author: cosin
pubDatetime: 2023-08-05T03:12:21+08:00 
title: islandjs 代码解析
postSlug: islandjs
featured: true
tags:
  - release
description: islandjs
---

# 介绍
Island.js 是一个基于 Vite、React 和 MDX 的静态站点生成器。

## 客户端运行时代码，
首先从最熟悉的客户端开始
```shell
packages/island/src/runtime/client-entry.tsx
```
```ts
renderInBrowser
```

## waitForApp
```ts
export async function waitForApp(routePath: string): Promise<PageData> {
  // 从路由进来的匹配的路径
  const matched = matchRoutes(routes, routePath)!;
  if (matched) {
    // Preload route component
    const matchedRoute = matched[0].route;
    // 疑问 react-router中的route路由应该是RouteObject，为何这里变为自定义的Route
    const mod = await (matchedRoute as Route).preload();
    // 删除锚点hash
    const pagePath = cleanUrl((matched[0].route as Route).filePath);

    const relativePagePath = getRelativePagePath(
      routePath,
      pagePath,
      // 一个全局对象 路由前缀
      siteData.base
    );
    // 判断是否为api页面
    const isApiPage =
      mod?.frontmatter?.api || mod?.frontmatter?.pageType === 'api';
    // API Page
    if (isApiPage) {
      const subModules = await Promise.all(
        routes
          .filter(
            (route: Route) =>
              route.path.startsWith(routePath) && route !== matchedRoute
          )
          .map(async (route: Route) => {
            const mod = await route.preload();
            return {
              ...mod,
              routePath: route.path
            };
          })
      );
      return {
        siteData,
        pagePath,
        relativePagePath,
        subModules,
        ...omit(mod, ['default']),
        pageType: 'api',
        routePath
      };
    } else {
      // 一些页面信息吧
      // Doc/Custom Page
      return {
        siteData,
        pagePath,
        relativePagePath,
        ...omit(mod, ['default']),
        pageType: mod?.frontmatter?.pageType || 'doc',
        routePath
      } as PageData;
    }
  } else {
    // 没有从路由匹配到对应的路由则返回
    // 404 Page
    return {
      siteData,
      pagePath: '',
      relativePagePath: '',
      pageType: '404',
      routePath: '/404'
    };
  }
}
```
