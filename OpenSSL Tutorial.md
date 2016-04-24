# OpenSSL命令用法，证书格式

## 术语解释
### 对称加密体系 Symmetric key (secret key) encryption
对称加密体系使用一把密钥同时用于加密和解密数据. 如下图所示， 数据通过安全密钥加密生成密文 **Chiper Text**,密文通过非安全的公共网络环境传输至接收者，接收者使用同一把安全密钥进行解密计算还原加密数据.
![](http://www.visolve.com/uploads/images/ssl_intro1.gif)

对称加密体系根据加密过程的不同，区分为下面两种类型:

* 基于分块 **block** 的加密算法: 将等待加密的数据分割为固定长度的块(**block**),对每个块单独的使用密钥进行加密运算.分块剩余的数据通过字符补齐为固定大小整数倍，额外进行单独加密.
* 基于流 **stream** 的加密算法: 通过伪随机数生成器(cryptographic pseudorandom number generators),使用一个固定的因子(seed)生成一串随机的比特位.这串比特位被称为密钥流(keystream).使用这个串密钥流与待加密数据进行亦或运算(xor)来记性数据加密.

其中 基于分块的加密算法应用最为广泛.

对称加密体系的安全级别取决于安全密钥的长度,密钥越长, 则被加密的数据越难被破解.但是同时， 密钥长度越长，则解密过程所需的执行时间也就越长，会导致整体性能的下降.

### 非对称加密体系 Asymmetric key (public key) encryption
非对称加密体系通过使用密钥对(key pair)来进行数据的加密和解密. 密钥对由一把公钥(public key)和一把私钥(private key)组成. 
当使用公钥进行加密时，只能使用私钥进行解密.同样，使用私钥进行解密时，只能通过公钥进行解密.

在同一台设备上， 对称密钥的解密运算速度比非对称秘钥快很多.所以现实中，两个加密体系经常结合进行使用: 是用非对称密钥对对称密钥进行加密用于密钥传递，而对称密钥则用于实际数据的加密.这个被称为混合加密体系，SSL使用的正是这种混合加密体系.

![](http://www.visolve.com/uploads/images/ssl_intro2.gif)
### 摘要 Digest
报文摘要(Digest)被用于保证数据是完整的且在传输的过程没有被篡改.摘要可以被认为是一长串实际报文的短小，长度固定的概要(summary).通常使用哈希算法(HASH)进行生成.基本上可以认为，两串不同的报文不可能具备同样的摘要.

在现实场景中， 原始数据及其摘要通常一同被加密传输至接收方.在对数据进行解密后，接收方对接收到的实际数据进行摘要计算,通过比较摘要来保证接收数据的完整性.


### 数字签名 digital signature
将该报文摘要值用发送者的私人密钥加密（对明文进行解密完全没问题，会得出一个不可读的“明文”），然后连同原报文一起发送给接收者，而“加密”后的报文即称数字签名.

接收方收到数字签名后，用同样的HASH算法对原报文计算出报文摘要值，然后与用发送者的公开密钥对数字签名进行解密（原先已经把签名加密了，再解密就能还原）得到的报文摘要值相比较.如相等则说明报文确实来自所称的发送者.
（由于只有拥有私钥的签名者能通过“解密”摘要生成签名，因此具有安全和不可抵赖性.）

那为什么是对报文摘要进行加密，而不是对原报文进行加密呢？这是因为RSA加解密非常耗时，被加密的报文越大，耗得时间越多，因此聪明的人类对其摘要进行加密，（因为报文摘要是要比原报文小得多），仍然能够起到同样的作用.这就是为什么多了个报文摘要.

### 证书 Certificates
证书是一种可以标明合法身份的文件，通常包含以下几个部分:

* 身份名称 machine/common name
* 机构/公司 organization/company
* 所在地 location of machine (city, state, and country)
* 证书的有效日期 time frame where certificate is valid
* 公钥/私钥 pblic key/private key for secure communication
* 证书颁发机构 Certificate authority name
* 证书签发方签名 Crtificate authority signature

其中,证书颁发机构(CA)是专门进行证书颁发和签名的第三方机构.它验证证书中信息的合法性.一个证书也可以进行自签(self-signed)，而不通过任何CA机构进行签名.但是在实际上，只有是通过被CA合法签名的证书才是被信任的.

#### 证书格式

### SSL Secure Socket Layer

## OpenSSL

## OpenSSL 常用命令
### 生成RSA密钥对
		openssl genrsa -aes128 -out fd.key 2048
* genrsa:生成RSA密钥，输出PEM格式密钥文件
* -aes128 密钥文件通过AES128进行加密保护
* -out 指定输出文件名
* 2048 生成的密钥长度

### 常看密钥文件结构
		openssl rsa -text -in fd.key
* -text 以文本格式输出密钥文件结构
* -in 指定被查看的密钥文件

### 导出公钥文件
		openssl rsa -in fd.key -pubout -out fd-public.key
* -puhout 指明导出密钥文件中的公钥部分,如果缺失该部分，则导出的为私钥部分

### 创建证书请求文件(CSR Certificate Signing Request)
		openssl req -new -key fd.key -out fd.csr
		
* -new 创建一个新的证书请求文件
* -key 指定密钥文件
* -out 指定输出的证书请求文件名

### 查看证书请求文件结构
		openssl req -text -in fd.csr -noout

### 从现有的证书导出证书请求文件
		openssl x509 -x509toreq -in fd.crt -out fd.csr -signkey fd.key
		
### 证书签名
从证书请求文件(csr)创建一个自签名证书(self-signed):

		openssl x509 -req -days 365 -in fd.csr -signkey fd.key -out fd.crt
		
你可以不必先创建csr文件，而是通过一条命直接创建自签名证书:
		
		openssl req -new -x509 -days 365 -key fd.key -out fd.crt
		
### 创建包含多组域名(主机名)的证书
		openssl x509 -req -days 365 -in fd.csr -signkey fd.key -out fd.crt -extfile fd.ext

其中:

* -days 指定证书的有效期(以天为单位)
* -extfile 指定扩展配置文件，其中包含额外的域名(主机名)配置，格式如下:

		subjectAltName = DNS:*.feistyduck.com, DNS:feistyduck.com 
		
		
## 密钥文件以及证书文件格式的转换
目前常见的证书以及密钥文件格式如下:

#### Binary(DER) cerificate
包含X509证书格式的原始内容，采用DER ANS1 编码格式
#### ASCII(PEM) cerificate
包含经过BASE64编码的DER格式证书, 以-----BEGIN CERTIFICATE-----作为文件头,以-----END CERTIFICATE-----作为文件结尾. 通常一个PEM文件通常只包含一个证书内容,但是其它一些程序允许PEM文件中根据上下文包含多个依赖证书信息. 
#### Binary(DER) key
包含密钥的原始格式内容，采用DER ANS1 编码格式
#### ASCII(PEM) key
包含经过BASE64编码的DER格式密钥信息.
#### PKCS#7 certificate(s)
通常以.p7b或者.p7c作为证书文件结尾.可以根据需要在一个PKCS#7文件中包含完整的证书串内容.该格式被JAVA的keytool工具所支持.
#### PKCS#12 (PFX) key and certificate(s)
通常以.p12或者.pfx作为证书文件结尾.通常被使用于微软Microsoft的各种产品，但是同时也用与客户端证书.


### PEM to DER
		openssl x509 -inform PEM -in fd.pem -outform DER -out fd.der
### DER to PEM
		openssl x509 -inform DER -in fd.der -outform PEM -out fd.pem
### PEM to PKCS#12
		openssl pkcs12 -export -name "My Certificate" -out fd.p12 -inkey fd.key -in fd.crt -certfile fd-chain.crt
### PKCS#12 to PEM
		openssl pkcs12 -in fd.p12 -out fd.pem -nodes
上诉命令转换的pem证书包含了密钥，证书信息，中间证书文件各个内容.可以通过人工或者下属命令进一步进行分割:

		openssl pkcs12 -in fd.p12 -nocerts -out fd.key -nodes 
		openssl pkcs12 -in fd.p12 -nokeys -clcerts -out fd.crt
		openssl pkcs12 -in fd.p12 -nokeys -cacerts -out fd-chain.crt

### PEM to PKCS#7
		openssl crl2pkcs7 -nocrl -out fd.p7b -certfile fd.crt -certfile fd-chain.crt
### PKCS#7 to PEM
		openssl pkcs7 -in fd.p7b -print_certs -out fd.pem
		
跟从PKCS#12转转PEM格式一样，需要通过人工或者命令的方式进一步分割出密钥，证书信息，中间证书文件各个内容.

		



		
		


