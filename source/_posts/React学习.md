---
title: React学习
date: 2019-12-24 23:24:56
categories:
- 2019
- 12月
tags:
- React
- 前端
---

### 基本概念

**React is a JavaScript library**

- 不是一个框架，不像 Angular
- 视图层的MVC应用

最重要的一点是React可以创建**components**， 像一个自定义，可复用的HTML元素，能快速有效的构建用户界面，使用`state`和`props`来存储数据

React使用`JSX`文件来编写：JavaScript + XML

```jsx
const heading = <h1 className="heading"> Hello React</h1>;
//实际上使用的是 createElement方法
const heading = React.createElement('h1', {className: 'heading'}, 'Hello React');
```

<!-- more -->

 https://www.taniarascia.com/getting-started-with-react/#components 

