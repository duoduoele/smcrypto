```
import { sm2, sm3, sm4 } from 'https://deno.land/x/smcrypto@v1.0.1/index.js';
```

## sm2

```
let {publicKey,privateKey} = sm2.generateKeyPairHex(); //生成公钥私钥

// let {publicKey,privateKey} = sm2.generateKeyPairHex('123123123123123'); //自定义随机数
// let {publicKey,privateKey} = sm2.generateKeyPairHex(256, new SecureRandom()); //自定义随机数，需要自行确保传入的随机数符合密码学安全

// let publicKey = sm2.getPublicKeyFromPrivateKey(privateKey);//根据私钥获取公钥

console.log('publicKey:',publicKey);
console.log('privateKey:',privateKey);


// 公钥压缩，默认生成公钥 130 位太长，可以压缩公钥到 66 位
const compressedPublicKey = sm2.compressPublicKeyHex(publicKey); // compressedPublicKey 和 publicKey 等价
console.log('compressedPublicKey:',compressedPublicKey);
console.log(sm2.comparePublicKeyHex(publicKey, compressedPublicKey)); // 判断公钥是否等价,返回true


let verifyResult = sm2.verifyPublicKey(publicKey); // 验证公钥,返回true
verifyResult = sm2.verifyPublicKey(compressedPublicKey); // 验证公钥,返回true


const cipherMode = 1; // 1 - C1C3C2，0 - C1C2C3，默认为1
let encryptData = sm2.doEncrypt('msgString', publicKey, cipherMode); // 加密结果，msgString: 'array'/'string'
console.log(encryptData);

// 密文会在解密时自动补充 '04'，如遇到其他工具补充的 '04' 需手动去除再传入。
let decryptData = sm2.doDecrypt(encryptData, privateKey, cipherMode, {output: 'array'}); // 解密结果，output: 'array'/'string' , 默认 string
console.log(decryptData);



// 纯签名 + 生成椭圆曲线点
let sigValueHex1 = sm2.doSignature('msgString', privateKey); // 签名
let verifyResult1 = sm2.doVerifySignature('msgString', sigValueHex1, publicKey); // 验签结果

// 纯签名
let sigValueHex2 = sm2.doSignature('msgString', privateKey, {
    pointPool: [sm2.getPoint(), sm2.getPoint(), sm2.getPoint(), sm2.getPoint()], // 传入事先已生成好的椭圆曲线点，可加快签名速度
}); // 签名
let verifyResult2 = sm2.doVerifySignature('msgString', sigValueHex2, publicKey); // 验签结果

// 纯签名 + 生成椭圆曲线点 + der编解码
let sigValueHex3 = sm2.doSignature('msgString', privateKey, {
    der: true,
}); // 签名
let verifyResult3 = sm2.doVerifySignature('msgString', sigValueHex3, publicKey, {
    der: true,
}); // 验签结果

// 纯签名 + 生成椭圆曲线点 + sm3杂凑
let sigValueHex4 = sm2.doSignature('msgString', privateKey, {
    hash: true,
}); // 签名
let verifyResult4 = sm2.doVerifySignature('msgString', sigValueHex4, publicKey, {
    hash: true,
}); // 验签结果

// 纯签名 + 生成椭圆曲线点 + sm3杂凑（不做公钥推导）
let sigValueHex5 = sm2.doSignature('msgString', privateKey, {
    hash: true,
    publicKey, // 传入公钥的话，可以去掉sm3杂凑中推导公钥的过程，速度会比纯签名 + 生成椭圆曲线点 + sm3杂凑快
});
let verifyResult5 = sm2.doVerifySignature('msgString', sigValueHex5, publicKey, {
    hash: true,
    publicKey,
});

// 纯签名 + 生成椭圆曲线点 + sm3杂凑 + 不做公钥推 + 添加 userId（长度小于 8192）
// 默认 userId 值为 1234567812345678
let sigValueHex6 = sm2.doSignature('msgString', privateKey, {
    hash: true,
    publicKey,
    userId: 'testUserId',
});
let verifyResult6 = sm2.doVerifySignature('msgString', sigValueHex6, publicKey, {
    hash: true,
    userId: 'testUserId',
});

let point = sm2.getPoint(); // 获取一个椭圆曲线点，可在sm2签名时传入
```

## sm3

```
let sm3Result = sm3('msgString'); //sm3杂凑
let sm3HashData = sm3('msgString', {
    key: 'daac25c1512fe50f79b0e4526b93f5c0e1460cef40b6dd44af13caec62e8c60e0d885f3c6d6fb51e530889e6fd4ac743a6d332e68a0f2a3923f42585dceb93e9', // 要求为 16 进制串或字节数组
}); //sm3杂凑+hash
```


## sm4

```
let sm4Key = sm4.generateKey(); //获取sm4的随机密钥

let sm4EncryptData_ecb = sm4.encrypt('msgString', sm4Key); // 加密，默认输出 16 进制字符串，默认使用 pkcs#7 填充（传 pkcs#5 也会走 pkcs#7 填充）
let sm4EncryptData_cbc = sm4.encrypt('msgString', sm4Key, {mode: 'cbc', iv: 'fedcba98765432100123456789abcdef'}); // 加密，cbc 模式

let sm4DdecryptData_ecb = sm4.decrypt(sm4EncryptData_ecb, sm4Key); // 解密，默认输出 utf8 字符串，默认使用 pkcs#7 填充（传 pkcs#5 也会走 pkcs#7 填充）
let sm4DdecryptData_cbc = sm4.decrypt(sm4EncryptData_cbc, sm4Key, {mode: 'cbc', iv: 'fedcba98765432100123456789abcdef'}); // 解密，cbc 模式

```
