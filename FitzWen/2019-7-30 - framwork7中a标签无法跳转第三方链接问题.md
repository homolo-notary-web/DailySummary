### framework7中a标签无法跳转第三方链接

```
  <a href="http://www.baidu.com">跳转</a>
```
这个时候谷歌浏览器下会报跨域

#### 解决方法：
给a标签添加external类名

```
  <a class="external" href="http://www.baidu.com">跳转</a>
```

这是为啥？咱也不知道，咱也不敢问啊