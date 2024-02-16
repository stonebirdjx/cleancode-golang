# 包注释

包注释紧贴package语句之上，且**只在一个文件中加入**，以`Package name `开头，以`.` 结尾，包注释内容可在`godoc`上显示。

**正例**

```
/*
Package zip provides support for reading and writing ZIP archives.
*/
package zip
```

> 内容较少的可以放在与包名同名的文件中，内容较多的放在`doc.go`文件中。