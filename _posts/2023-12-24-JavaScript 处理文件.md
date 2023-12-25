---
layout: post
title:  "JavaScript 处理文件"
date:   2023-12-25 17:00:00 +0800
author: Acan
keywords: HTML, JavaScript, file
categories: 

---

## ```input``` 标签

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

## File 对象

为了认识 file 对象，我们先介绍一些 JS 中的概念

### Blob 对象

Blob（二进制大对象）对象是 JS 中用于表示二进制数据的一种数据类型，通常用于处理文件或处理从其他来源获取的二进制数据，例如用户上传的文件或通过网络请求获取的数据。

Blob 对象可以通过如下代码创建：

```
var data = ["Hello, World! "]
var blob = new Blob(data, { type: 'text/plain' })
```

这段代码表示 blob 的数据类型为 ```text/plain```，数据的具体内容为“Hello World! ”。

可以通过如下代码将 blob 对象转换为 ArrayBuffer 类型：

```
const reader = new FileReader()

const blob = new Blob(["Hello World! "], { type: "text/plain" })

reader.onload = evt => {
    var arrayBuffer = evt.target.result
    console.log(arrayBuffer)
}

reader.readAsArrayBuffer(blob)
```

### File 对象

File 对象是 blob 对象的子类，换句话说，file 对象继承了 blob 对象。

#### 包装 file 对象

可以用如下代码将 blob 对象包装为 file 对象：

```
const blob = new Blob(["Hello World! "], {type: "text/plain"})

const file = new File([blob], "file.txt", {
    type: "text/plain",
    lastModified: new Date()
})
```

当然，file 对象不一定非得用 blob 对象包装而来，也可以直接将 Uint8Array 对象包装为 file 对象：

```
const encoder = new TextEncoder()

const uint8array = encoder.encode("Hello World! ")

const file = new File([uint8array], "file.txt", {
    type: "plain/text",
    lastModified: new Date()
})
```

以上两段代码获得的 file 对象是没有区别的。

#### 解析 file 对象

对 JS 默认的 FileReader 进行包装，从而使用 promise 对象解析 file 对象：

```
// 生成 file 对象

const encoder = new TextEncoder()
const file = new File([encoder.encode("Hello World! ")], "file.txt", {
    type: "file/plain",
    lastModified: new Date()
})

// 读取 file 数据（promise）

const readAsText_packed = file => {
    return new Promise((resolve, reject) => {
        const reader = new FileReader()
        reader.onload = evt => {
            const fileContent = evt.target.result
            resolve(fileContent)
        }
        reader.onerror = err => {
            reject(err);
        }
        reader.readAsText(file)
    })
}

// promise 调用

readAsText_packed(file).then(res => {
    console.log(res)
}).catch(err => {
    console.error(err)
})
```

以上代码将 file 解析为字符串，类似地也可通过 ```readAsArrayBuffer``` 函数将 file 对象解析为 ArrayBuffer 类型。

此外，也可以通过本文开始处的代码获取 file 对象的 ```lastModified``` 等属性。
