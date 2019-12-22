# 1> 安装 

`npm install --s crypto-js`

![CryptoJS](http://120.79.222.255/icon-crypto.png)

# 2> 封装公共JS

```js
import CryptoJS from 'crypto-js'

/**
 *  3DES 加解密
 *  key，iv -加密要与后端统一
 */
const key = CryptoJS.enc.Utf8.parse('123456789012345678901234');
const iv = CryptoJS.enc.Utf8.parse('01234567');

export default {
    /**
     *  解密方法
     */
    Decrypt (word) {
        const decrypted = CryptoJS.TripleDES.decrypt(word,
            key, {
                mode: CryptoJS.mode.CBC,
                padding: CryptoJS.pad.Pkcs7,
                iv: iv
        })
        return CryptoJS.enc.Utf8.stringify(decrypted).toString()
    },
    /**
     *  加密方法
     */
    Encrypt (word) {
        let words = CryptoJS.enc.Utf8.parse(word)
        const encrypted = CryptoJS.TripleDES.encrypt(words,
            key, {
                mode: CryptoJS.mode.CBC,
                padding: CryptoJS.pad.Pkcs7,
                iv: iv
        })
        return encrypted.toString()
    }
}
```

# 3> axios 在拦截器里进行加、解密

```js
/**
 *  Encrypt -加密
 *  1、在 request 拦截器实现, 传给后台的
 */
axios.interceptors.request.use( config => {
    const token = getStore('hasSessionToken');
    token && (config.headers.token = token);
    config.data = {
        data: CryptoJS.Encrypt(JSON.stringify(config.data))
    }

    return config
}, error => {

    return Promise.reject(error);
})

/**
 *  2、在 response 拦截器实现, 拿后台返回的
 *  Decrypt()             -解密
 *  getRealJsonData()     -去掉双引号，转化json格式
 */
axios.interceptors.response.use( response => {
    response.data = getRealJsonData(CryptoJS.Decrypt(response.data.data))

    if (response.data.code === -1) {
        Message.error({
            content: response.data.msg,
            duration: 3
        })
        removeStore('hasSessionToken')
        removeStore('hasUserName')
    }
    return response
}, error => {
    if (error.data) {
        switch (error.response.code) {
            case 400:
                err.message = '错误请求'
                break;
            case 404:
                err.message = '请求错误,未找到该资源'
                break;
            case 500:
                err.message = '服务器端出错'
                break;
            case 505:
                err.message = 'http版本不支持该请求'
                break;
            default:
                err.message = `连接错误${err.response.code}`
        }
    }
    return Promise.reject(error)
})
```