#  可输入DIV框
## 描述
> 使用<textarea>标签。但是标签高度不会随文本高度自动撑开，而是出现滚动条.

> 使用div或者p、span等非表单标签，通过赋予其contentEditable='true'属性，是其获得内容可以编辑的功能，从而使标签高度随着文本内容高度自动撑开


```
&[contenteditable=true] {
    &:empty:before {
        font-size: 0.28rem;
        line-height: 0.6rem;
        content: attr(placeholder);
        display: block;
        color: #ccc;
        }
    }
```

> 为了实现双向绑定，单独声明一个组件，在组件内部处理数据（也就是innerHTML），并将数据返回给父组件。然后在父组件中直接使用 v-model 就可以了

> 为了实现对字数的限制可以对keyup事件加上字数限制的方法

[组件地址](https://github.com/xpchen-fa/XP-Blog/blob/master/%E7%A7%BB%E5%8A%A8%E7%AB%AF/editdiv.vue)

## 参考资料
[可编辑DIV](https://segmentfault.com/a/1190000008261449)





