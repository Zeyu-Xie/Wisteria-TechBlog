---
layout: post
title:  "JavaScript 处理文件"
date:   2023-12-25 17:00:00 +0800
author: Acan
keywords: HTML, JavaScript, file
categories: 

---

## 从 ```input``` 标签上传的文件

HTML 中 file 类型的 ```input``` 标签如下使用：

```
<!-- 单文件 -->
<input id="input" type="file">

<!-- 多文件 -->
<input id="input" type="file" multiple>
```

通过如下 JS 代码处理（多文件 ```input```）上传的文件：

```
const _input = document.getElementById("input")
let file = null

_input.addEventListener("change", () => {

    file = _input.files[0]

    console.log(file)
    console.log("Name: " + file.name)
    console.log("Size: " + file.size)
    console.log("Last Modified: " + file.lastModified)
    console.log("Last Modified Date: " + file.lastModifiedDate)
    console.log("Type: " + file.type)
    console.log("Webkit Relative Path: " + file.webkitRelativePath)
    
})
```

由上可见，我们通过 DOM 节点的 ```files[i]``` 读取上传的第 i 个文件。

## ```file``` 对象的操作

为了认识 ```file``` 对象，我们先介绍一些 JS 中的概念

### ```blob``` 对象

```blob```（二进制大对象）对象是 JS 中用于表示二进制数据的一种数据类型，通常用于处理文件或处理从其他来源获取的二进制数据，例如用户上传的文件或通过网络请求获取的数据。

```blob``` 对象可以通过如下代码创建：

```
var data = ["Hello, World! "]
var blob = new Blob(data, { type: 'text/plain' })
```

这段代码表示 ```blob``` 的数据类型为 ```text/plain```，数据的具体内容为“Hello World! ”。

可以通过如下代码将 ```blob``` 对象转换为 ArrayBuffer 类型

```
const reader = new FileReader()

const blob = new Blob(["Hello World! "], { type: "text/plain" })

reader.onload = evt => {
    var arrayBuffer = evt.target.result
    console.log(arrayBuffer)
}

reader.readAsArrayBuffer(blob)
```



