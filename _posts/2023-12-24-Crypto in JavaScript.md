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

### 随机数据生成

```
const random = new Uint8Array(12)
crypto.getRandomValues(random)
console.log(random)
```

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

### 对称加密 AES

```
const encoder = new TextEncoder()
const decoder = new TextDecoder()

// 随机数

const iv = new Uint8Array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11])

// 明文

const data = "Hello World! "
const data_uint8array = encoder.encode(data)

// 密文

const cipher = new Uint8Array([102, 188, 155, 41, 51, 217, 72, 9, 176, 225, 194, 142, 2, 95, 226, 2, 147, 76, 57, 47, 173, 14, 92, 158, 153, 192, 199, 248, 242])

// 根据密码生成 16 位 Uint8Array 密钥数据

const password = "mypassword";
const password_uint8array_tmp = encoder.encode(password)
const password_length = password_uint8array_tmp.length

const password_uint8array = new Uint8Array(16)
password_uint8array.set(password_uint8array_tmp, 0)
for (let i = password_length; i < 16; i++) {
    password_uint8array[i] = 16 - password_length
}

// 根据密钥数据生成 CryptoKey 密钥（promise）

const uint8array_to_cryptokey = async password_uint8array_ => {
    return await crypto.subtle.importKey(
        "raw",
        password_uint8array_,
        { name: "AES-GCM", length: 256 },
        false,
        ["encrypt", "decrypt"]
    )
}

uint8array_to_cryptokey(password_uint8array).then(password_cryptokey => {

    // 加密生成 uint8array 密文

    crypto.subtle.encrypt(
        { name: "AES-GCM", iv: iv },
        password_cryptokey,
        data_uint8array
    ).then(encrypted_buffer => new Uint8Array(encrypted_buffer)).then(encrypted_uint8array => {
        console.log("Encrypted Uint8Array")
        console.log(encrypted_uint8array)
        console.log("")
    }).catch(err => {
        console.error(err)
    })

    // 解密生成 Uint8Array 明文

    crypto.subtle.decrypt(
        { name: "AES-GCM", iv: iv },
        password_cryptokey,
        cipher
    ).then(decrypted_buffer => {
        console.log("Decrypted Uint8Array")
        console.log(new Uint8Array(decrypted_buffer))
        console.log("")
    }).catch(err => {
        console.error(err)
    })

}).catch(err_ => {
    console.error(err_)
})

```

