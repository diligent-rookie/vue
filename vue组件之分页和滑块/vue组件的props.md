---
title: vue组件的props
tags: vue
notebook: 
---
## 问题来源：
在学习vue组件时，常使用vue组件制作一个分页效果，但是却出现的一些问题，在Vue_分页的页面中即使我利用vue制作分页的初版本，当我们点击左右方向符控制页码加减和点击页码实现跳转都没有问题，样式的改变也符合我想要的需求。但是！！但是！！！当通过input输入框输入页码实现页码跳转时，控制台却报如下警告：<br>
>```
>vue.js:597 [Vue warn]: Avoid mutating a prop directly since the value will be overwritten whenever the parent component re-renders. Instead, use a data or computed property based on the prop's value. Prop being mutated: "page"
>
>found in
>
>---> <Pa>
>       <Root>
>```
从这样的警告中我能看出来的只有他是"page"的问题，但是"page"问题在哪里，还是不明白，于是问题暂时搁置，继续学习vue官网
## 恍然大悟：
当继续vue的官方文档时，看到了这样一句话：
### props的单向数据流 ：
>所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解。
>额外的，每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值。这意味着你不应该在一个子组件内部改变 prop。如果你这样做了，Vue 会在浏览器的控制台中发出警告。
>这里有两种常见的试图改变一个 prop 的情形：
> * 这个 prop 用来传递一个初始值；这个子组件接下来希望将其作为一个本地的 prop 数据来使用。在这种情况下，最好定义一个本地的 data 属性并将这个 prop 用作其初始值：
> ```javascript
>    props: ['initialCounter'],
>    data: function () {
>    return {
>    counter: this.initialCounter
>        }
>    }
> ```
> * 这个 prop 以一种原始的值传入且需要进行转换。在这种情况下，最好使用这个 prop 的值来定义一个计算属性：
> ```javascript
>    props: ['size'],
>    computed: {
>        normalizedSize: function () {
>        return this.size.trim().toLowerCase()
>        }
>    }
> ```
>**注意在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变这个对象或数组本身将会影响到父组件的状态。**

惊呆了，我的小伙伴，这好像就是问题的真正所在，也就是props只能是单向数据流，要想实现双向数据流，我们得在data的函数里给它创建一个副本，然后组件里的用到props里的变量都要换成副本，但是这样问题就解决了吗？并没有，你会发现input输入控制页面实现了，但是！！！！我原来实现的需求呢，啊，怎么没了，有没有绝望╮(╯﹏╰）╭
## 上网看博客真的是一个好习惯：
当出现上面的情况，多次试验还是不能完善，让我开始怀疑创建副本的方式到底是否正确，但是毕竟那是人家官网说的啊，怎么可以否定呢，于是，我决定查查博客有关props的双向数据流实现。果然一篇文章拯救了我，来自http://www.cnblogs.com/xxcanghai/p/6124699.html的小小沧海的大佬，对此问题有了详细的解答，他拿一个开关的例子对问题进行了说明，在文章中他对props实现双向数据流给出了两步说明：
>* 在组件内的data对象中创建一个props属性的副本
>* 创建针对props属性的watch来同步组件外对props的修改

哇哇哇哇！！！第一步我们再上一个标题中解决了，但是我们好像没有监听这个副本，所以，没办法告知组件外的副本的变化，这也是为什么，我们通过点击方向符改变和点击页面没有使样式改变，所以我们应该在组件中加上watch事件对此进行监听，以便告知父组件副本发生变化，所以我们将起初页面改成VUE的分页效果的完善版.html文件，得到我们的最终版本。但是学习并没有停止，我们发现人家大佬的博客中还说明了需要props做双向绑定的情况，由于已把链接附上，我就不多次一举重复了，还有使props双向绑定处理的另一种方法mixin组件propsync方法，大家有兴趣都可以学学。