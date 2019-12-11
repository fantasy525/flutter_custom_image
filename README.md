# flutter_custom_image
 从flutter官方Image修改而来,在didChangeDependencies生命周期中增加了判断ImageProvider是否一致，如果一致就不会再去_resolveImage()
```dart
void didChangeDependencies() {
    ...
   if(widget.image != _oldImage) {
      _oldImage = widget.image;
      _resolveImage();
    }

    ...
}
```  
这么做的原因是因为在页面不可见的时候，如果你有路由操作，比如push,pop,那在页面栈下面的不可见页面也会走build方法，测试发现Image也会走didChangeDependencies，而官方Image内部会调用_resolveImage方法，这个方法后续的执行里会判断是否命中缓存，否则重新下载并解码图片，
在某些情况下，如果页面图片够多，而push了三四层页面，导致缓存大小耗尽，此时根据LRU算法会删除页面底部那些图片的缓存，那就会导致在页面不可见的时候也会触发页面去重新加载远程或者本地图片并交给c++解码，而根据闲鱼技术的文章分析了解到，大量的不可见图片resolveImage会导致c++那边内存占用过多
[如何进一步提高flutter内存表现](https://juejin.im/post/5bbec3d15188255c4322bbee#heading-15)
所以增加这个判断就是为了在页面不可见并且缓存没命中时别去在加载图片，减少了c++那里的内存压力，降低解码的次数，从而提高app性能

A new Flutter package project.

## Getting Started

This project is a starting point for a Dart
[package](https://flutter.dev/developing-packages/),
a library module containing code that can be shared easily across
multiple Flutter or Dart projects.

For help getting started with Flutter, view our 
[online documentation](https://flutter.dev/docs), which offers tutorials, 
samples, guidance on mobile development, and a full API reference.
