# blockchain-wallet-sdk

区块链钱包Node SDK，包含助记词生成，所有数字货币的地址私钥生成，私钥管理，交易签名等内容

## 一.助记词

目前支持生成12,15,18,21,24个不同语言的助记词，支持的语言有中文简体，中文繁体，英语，法语，日语，意大利语，韩语和西班牙语。库中也提供助记词的编解码和随机数种子的生成。

### 1.助记词生成

* 使用该库前需要先引入库

      var testWord = require("../sdk/mnemonic/generateWord");

下面简单地写几个案例

#### 1.1.生成12个中文简体助记词

    var chinese_simplified = testWord.createHelpWord(12, 'chinese_simplified');
    
    侧 钟 毕 蓝 祝 牢 炭 旬 岛 锦 裁 萧
    
#### 1.2.生成15个中文繁体助记词

    var words = testWord.createHelpWord(15, 'chinese_traditional');
    
    晚 錢 破 性 及 普 木 迷 矛 而 肅 揭 聖 擠 言
    
#### 1.3.生成18个日文助记词

    var words = testWord.createHelpWord(18, 'japanese');

    かくとく　たもつ　けっせき　ちたい　こんわく　ぐっすり　しちょう　たいふう　まける　しほん　にんそう　てくび　さつまいも　ちつじょ　だいじょうぶ　ひっこし　てんらんかい　むかい
    
*注：您要生成多少个助记词，您就传入数字几（目前支持：`12,15,18,21,24`）；后面一个传入相应的语言，目前可以传入的语言有：`chinese_simplified`（中文简体）；`chinese_traditional`（中文繁体）；`english`（英语）；`french`（法语）；`italian`（意大利语）；`japanese`（日语）；`korean`（韩语）；`spanish`（西班牙语）。

### 2.助记词编解码

    // 生成助记词
    var words = testWord.createHelpWord(18, 'japanese');
    console.log(words)
    
    // 助词编码
    var wordsEntropy = testWord.wordsToEntropy(words, 'japanese');
    console.log(wordsEntropy);
    
    // 助记词解码
    var wd = testWord.entropyToWords(wordsEntropy, 'japanese');
    console.log(wd);


### 4.验证助记词

    var bool = testWord.validateMnemonic(words, 'japanese');
    console.log(bool)  // 返回true表示验证成功
    
## 二.生成地址

目前该库支持以太坊，ERC20和比特币地址和私钥生成

### 1.生成BTC地址

下面代码是生成比特地址的代码

      var mnemonicS = require("../sdk/mnemonic/generateWord");
      var address = require("../sdk/address/generateAddress");

      var mnemonic= mnemonicS.createHelpWord(12, 'english');
      var seed = mnemonicS.mnemonicToSeed(mnemonic);
      var addressParmas = {
          "seed":seed,
          "coinType":"BTC",
          "number":"12",
          "bipNumber":"0",
          "receiveOrChange":"1",
          "coinMark":"BTC"
      }

      var addr = address.blockchainAddress(addressParmas);
      console.log(addr);

下面是生成效果：

      { 
        coinMark: 'BTC',
        privateKey: 'L3LK2uQQZCj7x7SzGSyRbiP8RwrYpGFPw7qbjuMKNfz83edmMJG3',
        address: '1AtMVGqbVSZDc4p5H414Q8zKqXMoYv7zxs' 
      }

上述代码中参数`seed`是随机数种子，这里需要传入的是一个`Buffer`流；`coinType`是要生成地址的币的类别，目前支持三种传参方式：`BTC`，`ETH`和`ERC20`;`number`是要生成第几个地址，`0`默认是第一个；`bipNumber`是该要生成地址的币在`BIP44`协议中的规范数字；`receiveOrChange`是比特币地址生成专用参数，`0`代表普通地址，`1`代表找零地址；`coinMark`是币的标识，例如`bitcoin`的标识是`BTC`，`ethereum`的标识是`ETH`。

### 2.生成以太坊地址

下面是生成以太坊地址的代码，参数如上

      var mnemonicS = require("../sdk/mnemonic/generateWord");
      var address = require("../sdk/address/generateAddress");

      var mnemonic= mnemonicS.createHelpWord(12, 'english');
      var seed = mnemonicS.mnemonicToSeed(mnemonic);
      var addressParmas = {
          "seed":seed,
          "coinType":"ETH",
          "number":"0",
          "bipNumber":"60",
          "receiveOrChange":"1",
          "coinMark":"ETH"
      }

      var addr = address.blockchainAddress(addressParmas);
      console.log(addr);
      
生成效果

      { 
            coinMark: 'ETH',
            privateKey: '612c02325cbf84cd4ad3dac8ae107e2bf37f98834b23f6f6208547f1a179d852',
            address: '0x947ee95a84f9bdf00dbc600961a737cb92fca5f4' 
      }

仔细观看不难看出，上面地址生成中，主要是参数改变了

### 3.生成单个ERC20地址

下面是生成ERC20地址的代码，参数如上

      var mnemonicS = require("../sdk/mnemonic/generateWord");
      var address = require("../sdk/address/generateAddress");

      var mnemonic= mnemonicS.createHelpWord(12, 'english');
      var seed = mnemonicS.mnemonicToSeed(mnemonic);
      var addressParmas = {
          "seed":seed,
          "coinType":"ERC20",
          "number":"0",
          "bipNumber":"518",
          "receiveOrChange":"1",
          "coinMark":"LET"
      }

      var addr = address.blockchainAddress(addressParmas);
      console.log(addr);


也只是传入参数发生变化

生成效果：

      { 
            coinMark: 'LET',
            privateKey: '0bdf145ae58a71c0da7fc3cf5510de5909d1314722f8d90c6b9ee543e431f1c7',
            address: '0x7d0c60a5ef87ed7f43fcad648911327c1f474623' 
      }

### 4.批量生成ERC20地址

      var mnemonicS = require("../sdk/mnemonic/generateWord");
      var address = require("../sdk/address/generateAddress");

      var mnemonic= mnemonicS.createHelpWord(12, 'english');
      var seed = mnemonicS.mnemonicToSeed(mnemonic);

      var ERC20AddressParam = {
          "seed":seed,
          "erc20":[
              {
                  "coinMark":"LET",
                  "bipNumber":"618",
                  "number":"0"
              },{
                  "coinMark":"SSP",
                  "bipNumber":"518",
                  "number":"0"
              },{
                  "coinMark":"Kcash",
                  "bipNumber":"128",
                  "number":"0"
              }
          ]
      }

      var addre= address.multiERC20AddressGenerate(ERC20AddressParam);
      console.log(addre);
      
如下是生成的效果

      [ 
            {
                  coinMark: 'LET',
                  privateKey: '164a6167e4c50e24ad6a74fd1cab6521e3a1472a70d67c88146a1952b7fb5092',
                  address: '0x3a960d90b219ea4b9d3f5827ea6fba5bf40d391f' 
           },
           { 
                  coinMark: 'SSP',
                  privateKey: 'f8f766b246289f325e32cb28462096ade51f89b720dc6b064168baef2e6c9ac4',
                  address: '0x8f5589faf64374788d1c8f3b1744bad1627ec565'
           },
           { 
                  coinMark: 'Kcash',
                  privateKey: '9bf8d4b890b391736cec94220d5d41d0c8a2b9555a5385316eecbeb89ce37d61',
                  address: '0x910b0d94dd1f0f173f41e5ed0a2ddefeea6b4130' 
          } 
      ]

## 三.以太坊Keystore

### 1.生成Keystore

生成keystore的只需要传入密码，将自动生成keystore

      var testKeystore = require("../sdk/keystore/generateKeystore");
      var keystore = testKeystore.createKeystore("123456");
      console.log(keystore);


      { 
            address: '531fe5d8be925ceba9bfce0c305d93fc6deb862b',
            crypto:
            { 
                  cipher: 'aes-128-ctr',
                  ciphertext: '3cf8ba317042815338f7397d80a53cf84cc7861c9ce70ed26fd23b2fdab9efc',
                  cipherparams: { iv: '82c3b2d97f65ff974563bd14d5edaf4f' },
                  mac: '67b2960dd73cd4f35d375a4c8c0898fc6f6cd9b7190dafb1c14aa724179a1e06',
                  kdf: 'pbkdf2',
                  kdfparams:
                  { 
                        c: 262144,
                        dklen: 32,
                        prf: 'hmac-sha256',
                        salt: 'cb7a82cba06330ffe0ad2fea7124034d53f5db559e30e7752f9562dab8caa39c
                   } 
             },
            id: 'a101bdc9-54d6-44a9-b787-587c92fbc680',
            version: 3
      }


### 2.导出keystore

      var testKeystore = require("../sdk/keystore/generateKeystore");

      var keystore = testKeystore.createKeystore("123456");
      console.log(keystore);
      testKeystore.exportKeystore(keystore, "./");

在你指定的目录下会生成相应的keystore文件

      UTC--2018-11-29T07-30-06.805Z--2fecb0dd3718453b51d7f42db6e9c60b2c82cfa9

### 3.导入keystore

      var keystore = testKeystore.importKeystore("531fe5d8be925ceba9bfce0c305d93fc6deb862b", "./");
      console.log(keystore);
      
      { 
            address: '531fe5d8be925ceba9bfce0c305d93fc6deb862b',
            crypto:
            { 
                  cipher: 'aes-128-ctr',
                  ciphertext: '3cf8ba317042815338f7397d80a53cf84cc7861c9ce70ed26fd23b2fdab9efc',
                  cipherparams: { iv: '82c3b2d97f65ff974563bd14d5edaf4f' },
                  mac: '67b2960dd73cd4f35d375a4c8c0898fc6f6cd9b7190dafb1c14aa724179a1e06',
                  kdf: 'pbkdf2',
                  kdfparams:
                  { 
                        c: 262144,
                        dklen: 32,
                        prf: 'hmac-sha256',
                        salt: 'cb7a82cba06330ffe0ad2fea7124034d53f5db559e30e7752f9562dab8caa39c
                   } 
             },
            id: 'a101bdc9-54d6-44a9-b787-587c92fbc680',
            version: 3
      }
      
 ### 4.导出私钥
 
      var privateKey = testKeystore.exportPrivateKey(keystore, "123456")
      
 ### 5.导入私钥
 
       var keystore = testKeystore.importPrivateKey("qeeqeqqeqwweq", "123456");
       console.log(keystore)
       
       { address: '805a06621171443bcf7d2bae9ac4c91539aef65f',
        crypto:
         { cipher: 'aes-128-ctr',
           ciphertext: 'f7c0ce4b3c83b9927ce75cbceb',
           cipherparams: { iv: '173ecf2cf4e34879175190a5a7b328cd' },
           mac: 'd8b829e98383cbc8eb69f090e765c085e0882006489c74837aa4bff69b7b62bc',
           kdf: 'pbkdf2',
           kdfparams:
            { c: 262144,
              dklen: 32,
              prf: 'hmac-sha256',
              salt: '85766bde5af062f631d62d16842d079e8a0a52a0555bfd94ce3f1b3c28fa839b'
       } },
        id: '70860efe-84f0-43c9-9d68-3f8f0262203b',
        version: 3 }

## 四.数字货币签名

