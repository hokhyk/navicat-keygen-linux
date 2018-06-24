# Navicat Keygen

  [中文版README](README.zh-CN.md)

  This repository will tell you how Navicat offline activation works.

## 1. Keyword Explanation.

  * __Navicat Activation Public Key__

    It is a __RSA-2048__ public key that Navicat used to encrypt or decrypt offline activation information.

    It is stored in __Navicat Premium.app/Contents/Resources/rpk__. You can see it by any kind of text editor. The concrete content is:

    > -----BEGIN PUBLIC KEY-----  
    > MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAw1dqF3SkCaAAmMzs889I  
    > qdW9M2dIdh3jG9yPcmLnmJiGpBF4E9VHSMGe8oPAy2kJDmdNt4BcEygvssEfginv  
    > a5t5jm352UAoDosUJkTXGQhpAWMF4fBmBpO3EedG62rOsqMBgmSdAyxCSPBRJIOF  
    > R0QgZFbRnU0frj34fiVmgYiLuZSAmIbs8ZxiHPdp1oD4tUpvsFci4QJtYNjNnGU2  
    > WPH6rvChGl1IRKrxMtqLielsvajUjyrgOC6NmymYMvZNER3htFEtL1eQbCyTfDmt  
    > YyQ1Wt4Ot12lxf0wVIR5mcGN7XCXJRHOFHSf1gzXWabRSvmt1nrl7sW6cjxljuuQ  
    > awIDAQAB  
    > -----END PUBLIC KEY-----  

    If you have the corresponding private key, please tell me. I would be very appreciated for your generous.

    __NOTICE:__

    Start from __Navicat Premuim 12.0.24 for Mac__, the public key is no longer stored in __Navicat Premium.app/Contents/Resources/rpk__. Instead, the public key is stored in Navicat executable file __Navicat Premium.app/Contents/MacOS/Navicat Premium__. You can see it by searching string `"-----BEGIN PUBLIC KEY-----  "`.

  * __Request Code__

    It is a Base64 string that represents 256-bytes-long data, while the 256-bytes-long data is the cipher text of the __offline activation information__ encrypted by __Navicat Activation Public Key__.

  * __Offline Activation Request Information__

    It is just a JSON-style ASCII string which contains 3 items. Respectively they are `"K"`, `"DI"` and `"P"`, which represent __snKey__, __DeviceIdentifier__ (related with your machine), __Platform__ (Appropriately speaking, it should be OS Type).

    Like:  
    > {  
    > &nbsp;&nbsp;&nbsp;&nbsp;"K": "xxxxxxxxxxxxxxxx",  
    > &nbsp;&nbsp;&nbsp;&nbsp;"P": "Mac 10.13",  
    > &nbsp;&nbsp;&nbsp;&nbsp;"DI": "xxxxxxxxxxxxxxxxxxxx"  
    > }  

  * __Activation Code__

    It is a Base64 string that represents 256-bytes-long data, while the 256-bytes-long data is the cipher text of the __offline activation response information__ encrypted by __Navicat Activation Private Key__ (so far, we don't know official activation private key).

  * __Offline Activation Response Information__

    Just like __Offline Activation Request Information__, it is also a JSON-style ASCII string. But it contains 5 items. Respectively they are `"K"`, `"N"`, `"O"`, `"T"`, '`DI`'.

    `"K"` and `"DI"` has the same meaning mentioned in __Offline Activation Request Information__ and must be same with the corresponding items in __Offline Activation Request Information__.

    `"N"`, `"O"`, `"T"` represent __Name__, __Organization__, __Time__ respectively. __Name__ and __Organization__ are string and the type of __Time__ can be string or integer (Thanks for discoveries from @Wizr, issue #10).

    Differ from Navicat Windows version, `"T"` is mandatory and must be -1 ~ +4 days difference from current time. Here is an example of __Offline Activation Response Information__:

    > {  
    > &nbsp;&nbsp;&nbsp;&nbsp;"DI" : "xxxxxxxxxxxxxxxxxxxx",  
    > &nbsp;&nbsp;&nbsp;&nbsp;"T" : "1515770827.925012",  
    > &nbsp;&nbsp;&nbsp;&nbsp;"K" : "xxxxxxxxxxxxxxxx",  
    > &nbsp;&nbsp;&nbsp;&nbsp;"N" : "DoubleLabyrinth",  
    > &nbsp;&nbsp;&nbsp;&nbsp;"O" : "Shadow"  
    > }  

  * __snKey__

    It is a 4-block-long string, while every block is 4-chars-long.

    __snKey__ is generated by 10-bytes-long data. In order to explain it easily, I use __data[10]__ to represent the 10-bytes-long data.

    1. __data[0]__ and __data[1]__ must be `0x68` and `0x2A` respectively.

       _`May change when Navicat product changes. Uncertain yet.`_  

    2. __data[2]__, __data[3]__ and __data[4]__ can be any byte. Just set them whatever you want.

    3. __data[5]__ and __data[6]__ are related with your Navicat product language. It depends.

       ~~_`May change when Navicat product changes. Uncertain yet.`_~~  
       _`Must change when Navicat product changes. Confirmed yet.`_

       |  Language  |  data[5]  |  data[6]  |  Discoverer     |
       |------------|-----------|-----------|-----------------|
       |  English   |  0xAC     |  0x88     |                 |
       |  简体中文   |  0xCE     |  0x32     |                 |
       |  繁體中文   |  0xAA     |  0x99     |                 |
       |  日本語     |  0xAD     |  0x82     |  @dragonflylee  |
       |  Polski    |  0xBB     |  0x55     |  @dragonflylee  |
       |  Español   |  0xAE     |  0x10     |  @dragonflylee  |
       |  Français  |  0xFA     |  0x20     |  @Deltafox79    |
       |  Deutsch   |  0xB1     |  0x60     |  @dragonflylee  |
       |  한국어     |  0xB5     |  0x60     |  @dragonflylee  |
       |  Русский   |  0xEE     |  0x16     |  @dragonflylee  |
       |  Português |  0xCD     |  0x49     |  @dragonflylee  |

       According to __Navicat 12 for Mac x64__ version, what IDA 7.0 indicates is that this two bytes are product signature.

    4. __data[7]__ represents whether it is __commercial license__ or __non-commercial license__.

       For __Navicat 12 x64__: `0x65` is __commercial license__, `0x66` is __non-commercial license__.  
       For __Navicat 11 x64__: `0x15` is __commercial license__, `0x16` is __non-commercial license__.  

       _`May change when Navicat product changes. Uncertain yet.`_  
       _`Must change when version change.`_  

       According to __Navicat 12 for Mac x64__ version, what IDA 7.0 indicates is that commercial license is __Enterprise License__ and non-commercial license is __Educational License__.

    5. High 4 bits of __data[8]__ represents __version number__. Low 4 bits is unknown, but we can use it to delay activation deadline. Possible value is `0000` or `0001`.

       For __Navicat 12 x64__: High 4 bits must be `1100`, which is the binary of number `12`.  
       For __Navicat 11 x64__: High 4 bits must be `1011`, which is the binary of number `11`.  

    6. __data[9]__ is unknown, but you can set it `0xFD` or `0xFC` or `0xFB` if you want to use __not-for-resale license__. This must not be `0x00`. But other value is OK.

       According to __Navicat 12 for Mac x64__ version, what IDA 7.0 indicates is that:

       * `0xFB` is __Not-For-Resale-30-days__ license.  
       * `0xFC` is __Not-For-Resale-90-days__ license.  
       * `0xFD` is __Not-For-Resale-365-days__ license.  
       * `0xFE` is __Not-For-Resale__ license.  
       * `0xFF` is __Site__ license.  

    -----------------

    After that. Navicat use __DES__ with __ECB mode__ to encrypt the last 8 bytes which are from __data[2]__ to __data[9]__.

    The DES key is:

    ```cpp
    unsigned char DESKey = { 0x64, 0xAD, 0xF3, 0x2F, 0xAE, 0xF2, 0x1A, 0x27 };
    ```

    Then encode the 10-bytes-long data:

    1. Regard __data[10]__ as a 80-bits-long data.

       If __data[10]__ starts with `0x68` and `0x2A`, so the 80-bits-long data is `01011000 00101010......`

    2. Divide the 80-bits-long data as 16 5-bits-long blocks.

       If __data[10]__ starts with `0x68` and `0x2A`, so the 80-bits-long data is `01011`, `00000`, `10101`, `0....`, ...

    3. So the value every block is less than 32. Map them by a encode-table:

       ```cpp
       // Thanks for discoveries from @Wizr, issue #10
       char EncodeTable[] = "ABCDEFGH8JKLMN9PQRSTUVWXYZ234567";
       ```

       Then you will get a 16-char-long string.

       If __data[10]__ starts with `0x68` and `0x2A`, so after encoded, it should starts with `"N"`, `"A"`, `"V"`.

    4. Divide the 16-char-long string to four 4-chars-long blocks, Then you get __snKey__.

## 3. Activation Process

  1. Check whether __sn_Key__ that user inputs is legal.

  2. After user clicks `Activate`, Navicat will start online activation first. If fails, user can choose offline activation.

  3. Navicat will use the __snKey__ that user inputs and some information collected from user's machine to generate __Offline Activation Request Information__, then encrypt it by __Navicat Activation Public Key__ and return Base64-encoded string as __Request Code__.

  4. In legal way, the __Request Code__ should be sent to Navicat official activation server by a Internet-accessible computer. And Navicat official activation server will return a legal __Activation Code__.

     But now, we use keygen to play the official activation server's role.

     1. According to the __Request Code__, Get `"DI"` value and `"K"` value.

     2. Fill __Offline Activation Response Information__ with `"K"` value, name, organization name and `"DI"` value.

     3. Encrypt __Offline Activation Response Information__ by __Navicat Activation Private Key__ and you will get 256-byte-long data.

     4. Encode 256-byte-long data by Base64. The result is __Activation Code__.

     5. Input __Activation Code__, then offline activation is done.

## 4. How to build

  * Before you build keygen, you should make sure you have installed OpenSSL.  
    If you have `brew`, you can install it by `brew install openssl`.

    ```bash
    $ cd navicat-keygen
    $ make release
    ```

  * Build patcher if your Navicat version is or is after __12.0.24__.

    ```bash
    $ cd navicat-patcher
    $ make release
    ```

    __NOTICE:__

    For Navicat whose version is or is after __12.0.24__, if you want to use your own RSA key, please replace the content in `/navicat-patcher/main.c`

    ```cpp
    const char pubkey[9][72] = {
    "-----BEGIN PUBLIC KEY-----",
    "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxqkTcfbKw8ysVygePlcB",
    "oUAhCF6oniyP13iDtu85ZsHwqw8PnMyTp6n6FnMN9YinleIAy6NFveBu/vshTN8S",
    "oXbYyy5AqdZ8CQpfvuriO9UNfgV1l7SFdPPpruFAmOw+uzA3GawMsg3QNK/htqJe",
    "b4xKHFS04xC2AueE2RTmk6tJcL8TEBfRG7DEYOHPjebKl1NQ3ZIu15U97cCPYKO2",
    "pWHzsb+Fr4Wj0DChLoxlXxaBcJ2ozogaq0tW2t4Aopvt9kRSuSK9HcgxICJM5ct4",
    "naU91WFGWlw0+0JpiMIl5OnMbpak/5xQre9DL8zM8LjRy14I88txvXvhPEsWaYCO",
    "1QIDAQAB",
    "-----END PUBLIC KEY-----"
    };
    ```

    with your own RSA public key before you build patcher.

## 5. How to Use
  1. Build keygen.

  2. Generate RSA-2048 private key and public key. __(Navicat Premium version < 12.0.24 ONLY)__

     ```bash
     $ openssl genrsa -out 2048key.pem 2048
     $ openssl rsa -in 2048key.pem -pubout -out rpk
     ```

     You will get two file: `2048key.pem` and `rpk`.

     Now you do not need to generate RSA key. I've already prepared these two file:

       * `rpk` file is in `navicat-patcher` folder.

       * `2048key.pem` is in `navicat-keygen` folder.

  3. For Navicat Premium version < 12.0.24:

       * Replace `Navicat Premium.app/Contents/Resources/rpk` file by `rpk` file.

     For Navicat Premium version >= 12.0.24:

       * Backup your `Navicat Premium.app/Contents/MacOS/Navicat Premium` and __all of your saved database connection configurations (with password)__.

       * Delete all of passwords in `Keychain.app` that is saved by Navicat.

       * Run patcher:

         ```bash
         $ cd navicat-patcher
         $ ./navicat-patcher <your navicat executable file path>
         ```

         Example:

         ```bash
         $ cd navicat-patcher
         $ ./navicat-patcher /Applications/Navicat\ Premium.app/Contents/MacOS/Navicat\ Premium
         ```

       * __Generate a self-signed code-sign certificate and always trust it. Then use `codesign` to re-sign `Navicat Premium.app`.__

         ```bash
         $ codesign -f -s "Your self-signed code-sign certificate name" <path to Navicat Premium.app>
         ```

  4. Then goto `navicat-keygen` folder and in Terminal:

     ```bash
     $ ./navicat-keygen 2048key.pem
     ```

     You will get a __snKey__ and be asked to input your name and organization.  
     Just input and then you will be asked to input the request code. Now DO NOT CLOSE KEYGEN.

  5. Open Navicat Premium, find and click `Registration`. Then input `Registration Key` by __snKey__ that keygen gave. Then click `Activate`.

  6. Generally online activation will failed and Navicat will ask you do `Manual Activation`, just choose it.

  7. Copy your request code and paste it in keygen. Leave empty line to tell keygen that your input ends (in other words, type `Enter` at least twice).

  8. Then you will get activation code which looks like a Base64 string. Just copy it and paste it in Navicat `Manual Activation` window, then click `Activate`. If nothing is wrong, activation should be done successfully.

  9. Finally, restore your database connection configurations if you have.
