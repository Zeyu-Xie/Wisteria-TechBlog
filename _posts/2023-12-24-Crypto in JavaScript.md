---
layout: post
title:  "Crypto in JavaScript"
date:   2023-12-24 22:41:00 +0800
author: Acan
keywords: JavaScript, crypto, encrypt
categories: 
---

## 介绍

Crypto 在这里指浏览器原生的 Crypto API，可以进行一系列哈希或加密运算。

Crypto 只能在浏览器环境下运行，NodeJS 环境下可以使用 crypto-js 包。

## 示例代码

为了统一及普适性，以下内容均以 Uint8Array 类型的操作为例。

（若忘记了 Uint8Array 的相关知识，可参考[这篇文章](https://zeyu-xie.github.io/Wisteria-TechBlog/2023/12/24/字符集-编码和二进制数据.html)）

### 计算 SHA-1 (Not Safe) / SHA-256 / SHA-384 / SHA-512

``````
const encoder = new TextEncoder()

async function getSHA256(uint8array_) {
    const buffer_ = await crypto.subtle.digest('SHA-256', uint8array_)
    const array_ = Array.from(new Uint8Array(buffer_))
    const string_ = array_.map(byte => byte.toString(16).padStart(2, '0')).join('')
    return string_;
}

const string_1 = "Hello World! "
const uint8array_1 = encoder.encode(string_1)

getSHA256(uint8array_1).then(SHA256_1 => {
    console.log(SHA256_1)
}).catch(err => {
    console.log("ERROR", err)
})
``````