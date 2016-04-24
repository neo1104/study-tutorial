# 加密机工具hsmutil使用手册,基于江南科友SJJ1214加密机

## 命令格式
	hsmutil [COMMON OPTIONS] COMMAND [COMMAND OPTIONS]

## 参数解释
	-a,--addr: 		连接加密机地址，默认地址: 192.168.13.109
	-p,--port: 		连接加密机端口，默认端口: 8
	-t,--timedout: 	通讯超时时间(单位s), 默认:10s
	--help:			显示帮助信息
	--zmk1:			分量1
	--zmk2: 		分量2
	--zmk:  		ZMK密文
	--zek:	 		ZEK密文
	--data:			加密数据
	--key-type:		密钥类型(zpk, zak, zek)

## 命令说明

### 1.生成在指定zmk下加密的随机密钥
	hsmutil GENKEY --key-type <zpk|zak|zek> --zmk <zmk>
### 2.根据分量生成ZMK
	hsmutil GENZMK --zmk1 <分量1> --zmk2 <分量2>
### 3.根据分量生成ZPK
	hsmutil GENZPK --zmk1 <分量1> --zmk2 <分量2>
### 4.根据分量生成ZAK
	hsmutil GENZAK --zmk1 <分量1> --zmk2 <分量2>
### 5.根据分量生成ZEK
	hsmutil GENZEK --zmk1 <分量1> --zmk2 <分量2>
### 6.加密数据
	hsmutil ENCRYPT --zek <zek> --data <待加密数据>
### 7.解密数据
	hsmutil DECRYPT --zek <zek> --data <待解密数据>
	
	