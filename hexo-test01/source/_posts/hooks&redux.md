---
title: react hooks与redux学习笔记
tags: [React]
categories: learning
toc: true
mathjax: true
---
  react hooks与redux学习笔记
 <!-- more -->

 #### 拜拜class组件 

 Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性,并且Hook 也不能在 class 组件中使用。

 Hook 将组件中相互关联的部分拆分成更小的函数，并不是像在class组件中按照生命周期来划分，也避免了一大堆的无关代码都堆在同一块中难以维护

 其实一直以来，我都觉得相比于Vue和Angular，在React中编写组件更像是在写函数，但是又像Vue与Angular一样将组件定义在class，而既然是在class中，就避免不了没完没了的this。而现在，Hook 的出现，得以让我们可以彻底地去拥抱函数式组件。

 #### Hook 初步使用



useState 会返回一对值：当前状态和一个让你更新它的函数

```
  const [count, setCount] = useState(0);

```

useEffect 给函数组件增加了操作副作用的能力。它跟 class 组件中的 componentDidMount、componentDidUpdate 和 componentWillUnmount 具有相同的用途，相当于给函数式组件也赋予了与生命周期相同的功能，它会在每次渲染后都执行，如果在 useEffect中 返回一个函数，React 将会在执行清除操作时调用它

```
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

```

通知 React 跳过对 useEffect 的调用，只要传递数组作为 useEffect 的第二个可选参数即可

```
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新

```



 #### 未完待续
