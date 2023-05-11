# chain33-ca

chain33联盟链CA服务节点，可向区块链用户签发认证证书，向区块链节点提供证书链认证，提供区块链用户权限管理和证书管理。 

用户在chain33联盟链发送交易交易前，先通过管理员在chain33-ca添加用户权限，用户向chain33-ca申请认证证书，然后再附加在交易签名中发送到区块链节点；节点通过chain33-ca提供的证书链校验用户证书的有效性。

# 运行
``` shell
wget https://chain33.oss-cn-hangzhou.aliyuncs.com/chain33-Ca.tar.gz

tar xzvf chain33-Ca.tar.gz

cd chain33-Ca

./chain33-ca -f chain33.ca.toml
```

# 使用方法
结合chain33-sdk

```java
// chain33-ca节点
static RpcClient certclient = new RpcClient("http://127.0.0.1:11901");

// 管理员注册用户
boolean result = certclient.certUserRegister(UserName, Identity, UserPub, AdminKey);

// 用户申请证书
CertObject.CertEnroll cert = certclient.certEnroll(Identity, UserKey);

// 构造交易
CertService.CertAction.Builder builder = CertService.CertAction.newBuilder();
builder.setTy(CertUtils.CertActionNormal);
byte[] reqBytes = builder.build().toByteArray();

String transactionHash = TransactionUtil.createTxWithCert(UserKey, "cert", reqBytes, SignType.SM2, cert.getCert(), "ca 
test".getBytes());

// 发送交易
String hash = chain33client.submitTransaction(transactionHash);
```

# 接口说明

## 注册用户
#### RegisterUser

**函数原型：**

```go
func RegisterUser(req *common.ReqRegisterUser, result *interface{}) error
```

**请求参数：**

|参数|类型|是否必填|说明|
|----|----|----|----|
|name|string|是|用户名|
|identity|string|是|用户ID|
|pubKey|string|是|用户公钥|
|sign|[]byte|是|管理员签名|

**返回字段：**

|返回字段|字段类型|说明|
|----|----|----|
|res|bool|注册结果|

## 注销用户
#### RevokeUser

**函数原型：**

```go
func RevokeUser(req *common.ReqRevokeUser, result *interface{}) error
```

**请求参数：**

|参数|类型|是否必填|说明|
|----|----|----|----|
|identity|string|是|用户ID|
|sign|[]byte|是|管理员签名|

**返回字段：**

|返回字段|字段类型|说明|
|----|----|----|
|res|bool|注销结果|

## 证书申请
#### Enroll

**函数原型：**

```go
func Enroll(req *common.ReqEnroll, result *interface{}) error
```

**请求参数：**

|参数|类型|是否必填|说明|
|----|----|----|----|
|identity|string|是|用户ID|
|sign|[]byte|是|用户签名|

**返回字段：**

|返回字段|字段类型|说明|
|----|----|----|
|serial|string|证书序列号|
|cert|[]byte|证书格式化输出|


## 用户证书重新申请，用于用户证书被注销后
#### ReEnroll

**函数原型：**

```go
func ReEnroll(req *common.ReqEnroll, result *interface{}) error
```
**请求参数：**

|参数|类型|是否必填|说明|
|----|----|----|----|
|identity|string|是|用户ID|
|sign|[]byte|是|管理员签名|

**返回字段：**

|返回字段|字段类型|说明|
|----|----|----|
|serial|string|证书序列号|
|cert|[]byte|证书格式化输出|


## 用户证书注销
#### RevokeCert

**函数原型：**

```go
func RevokeCert(req *common.ReqRevokeCert, result *interface{}) error
```

**请求参数：**

|参数|类型|是否必填|说明|
|----|----|----|----|
|serial|String|否|证书序列号，通过序列化注销|
|identity|String|否|用户ID，通过用户ID注销|
|sign|[]byte|是|管理员签名|

**返回字段：**

|返回字段|字段类型|说明|
|----|----|----|
|res|bool|注销结果|
