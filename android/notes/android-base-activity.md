Android转场动画
==================================================


Android界面开发

- [Android中的shape](https://github.com/addcn/ideas/blob/master/android/notes/android-base-shape.md)
- [Android中的selector](https://github.com/addcn/ideas/blob/master/android/notes/android-base-selector.md)
- [Android中align](https://github.com/addcn/ideas/blob/master/android/notes/android-base-align.md)
- [Android中的style](https://github.com/addcn/ideas/blob/master/android/notes/android-base-style.md)
- [Android中的inflate](https://github.com/addcn/ideas/blob/master/android/notes/android-base-inflate.md)
- [Android中的常用布局](https://github.com/addcn/ideas/blob/master/android/notes/android-base-layout.md)
- [Android中常用代码](https://github.com/addcn/ideas/blob/master/android/notes/android-base-code.md)
- [Android中view绘制流程](https://github.com/addcn/ideas/blob/master/android/notes/android-base-view.md)
- [Android中的adapter](https://github.com/addcn/ideas/blob/master/android/notes/android-base-adapter.md)
- [Android中的sqlite](https://github.com/addcn/ideas/blob/master/android/notes/android-base-sqlite.md)

----------


1.res文件下anim文件夹，新建fade_in.xml、fade_out.xml文件



跳转时调用

```

startActivity(intent);

finish();

overridePendingTransition(R.anim.fade_in,R.anim.fade_out);

```


关闭时调用


```

finish();

overridePendingTransition(R.anim.fade_in,R.anim.fade_out);

```





参考文章

> [Activity 页面淡入淡出](http://www.jianshu.com/p/8e74e980bf03)

> [动起来！动起来！- Android Transitions 转场动画](http://www.jianshu.com/p/98f2ec280945#)

> [实现activity跳转动画的五种方式](http://blog.csdn.net/qq_23547831/article/details/51821159)

