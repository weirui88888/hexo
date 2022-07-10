# hexo使用步骤

```javascript
hexo s 启动本地服务，进行开发
hexo s --draft 启动本地服务，并且支持预览草稿

hexo new xxx 大部分时候用来创建博客 等同于 hexo new post xxx，会使用scaffolds/post作为模板
hexo new draft xxx 创建草稿博客，放到source/draft中，执行generator时，会忽略草稿中的文件
如果草稿写完了，可以通过hexo p <filename> 将草稿移动到_posts下
```

# 开发技巧
为了做一些兼容性的工作排版布局，在主题的样式文件中，添加了一些自定义的样式，比如：
- 手绘字体添加自定义class类名painted
- shields-container容器中的小图标变成行内块元素