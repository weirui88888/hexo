# hexo使用步骤

```javascript
hexo s 启动本地服务，进行开发
hexo s --draft 启动本地服务，并且支持预览草稿

hexo new xxx 大部分时候用来创建博客 等同于 hexo new post xxx，会使用scaffolds/post作为模板
hexo new draft xxx 创建草稿博客，放到source/draft中，执行generator时，会忽略草稿中的文件
如果草稿写完了，可以通过hexo p <filename> 将草稿移动到_posts下
```