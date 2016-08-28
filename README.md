###设置 UItableViewCell 分割线

关于UITableViewCell的分割线，网上有无数的教程，，但是许多真的用起来却毫无作用。这让很多人最后只能隐藏系统分割线，自己画一条。

我也遇到了这个坑，又不太甘心采用自己画线的方法。在阅读一些资料后，大概整理了下，分享出来，希望以后更多的的人可以绕过这个坑。

很多网上搜索来的解决分案，大多是，设置separatorInset属性（iOS7 之后加入的），这个属性定义了分割线到左右边界的距离（注：separatorInset虽然是UIEdgeInsets类型，但是只有左右属性有效）。 实际上，在iOS7确实是这样设置确实有效，这也是为什么很多blog给出这样的方案。

可，现在实际上，这并不一定能行得通，很多时候你会发现，这个属性并没有达到预期的效果。原因是iOS8之后增加了这样一个属性preservesSuperviewLayoutMargins，它的作用是 设置视图是否保留父视图的margins。这里有一个坑，也是上面的解决方案时灵时不灵的根源。
`@property (nonatomic) BOOL preservesSuperviewLayoutMargins NS_AVAILABLE_IOS(8_0); // default is NO - set to enable pass-through or cascading behavior of margins from this view’s parent to its children`
这是官方的注释，默认是NO，即不保留父视图约束。然而，实际情况并不一定是这样。经过验证，在UITableViewCell 加载方式不同时，这个属性的默认值并不同。

1. cell如果是IB加载，preservesSuperviewLayoutMargins 默认为NO，而如果是代码加载，默认值则为YES。这就是这个坑之所在。

2. iOS8之后，加入了layoutMargins属性，layoutMargins定义了视图边界与子视图边界之间间距，非控制器根视图默认为UIEdgeInsetsMake(8.0, 8.0, 8.0, 8.0)，即上下左右各8个单位长度。控制器根视图默认为UIEdgeInsetsMake(0.0, 16.0, 0.0, 16.0)或者UIEdgeInsetsMake(0.0, 20.0, 0.0, 20.0)，取决于当前size class。我们可以改变非根视图的layoutMargins，但是不可以改变根视图的layoutMargins，它由系统设置管理。

扯了这么多,我们来总结下:

1. 建议在 cell设置`cell.preservesSuperviewLayoutMargins = NO;`,舍得区分 xib 和代码创建
2. 设置`cell.layoutMargins = UIEdgeInsetsZero;`,将系统预留的间距去掉
3. 设置你需要的边距`cell.separatorInset = UIEdgeInsetsMake(0, 0, 0, 0);`

  1. 比如你要设置隐藏,就可以设置左边的偏移量为一个屏幕的宽度
  2. 比如你要设置没有间距,就设置左边的偏移量为0
  3. separatorInset可以在UITableView进行全局设置

**效果图**

<figure class="third">
    <img src="http://upload-images.jianshu.io/upload_images/874748-41a7d0ec65c1a0bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/200">
    <img src="http://upload-images.jianshu.io/upload_images/874748-0af74a30f3b788db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/200">
    <img src="http://upload-images.jianshu.io/upload_images/874748-135499275b8f783b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/200">
    
</figure>


