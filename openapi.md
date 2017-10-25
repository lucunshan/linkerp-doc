# 授权接口
## 开发前必读
###授权信息获取方式
请扫码申请试用，获取企业appId，secrett,AES加密Key等信息。企业授权信息需妥善保管，防止泄露。
<div align="right">
<img src="http://wx2.sinaimg.cn/mw690/8105c641gy1fkc2i685wzj20730730g0.jpg" " height="175" width="161" >
</div>
###示例代码
[Java Demo](https://github.com/lucunshan/linkerp-demo)   [.Net Demo](https://github.com/lucunshan/linkerp4Net-demo)
###API通用约定
1. 访问域名：https://yunzhijia.com
2. 接口调用URL：https://域名/接口地址?accessToken=xxxxx，accessToken是访问接口的令牌，每个令牌都只能访问对该令牌授权过的接口
3. 调用方式：Post JSON 
4. 数据格式：

- 参数：
```
{
    "data": “AES加密后参数信息""
}
示例：
{
    "data": "aQeU3beqnhk6gP7V3fXELrk9a/9vHHfXXNSTGlOOq/eu4/0p3rkaEyKw/L2214sVPEbFNQSucs1ke6E9WBu0uw=="
}
```
 - 返回值
```
{
    "data": "AES加密后结果",
    "success": 是否成功  //true,false,
    "error": "错误信息， //成功时为空",
    "errorCode": 错误码， //成功时为0
}

示例：
{
    "data": "Ajl2Iobg0+OGjRMnSEzWGQ5RwhfHUMCMQWCU31d6F9s=",
    "success": true,
    "error": "鉴权信息数据获取失败!",
    "errorCode": 401
}
```

# OAUTH2.0授权接口
### 签名算法
使用sha1将参数使用ASCII 码从小到大排序（字典序）后生成签名
```java
Object[] list = new Object[]{"param1","param2","param3"};
Arrays.sort(list);
String sign = DigestUtils.shaHex(StringUtils.join(list));
```
例如接口请求参数为：{appId:xxxx,nonceStr:xxxx,signature:xxxx,timestamp:xxxx}
```java
String appId = "xxxx",secret = "xxxxx",nonceStr ="xxxx",timestamp = "xxxxx";
Object[] list = new Object[]{appId,secret,nonceStr,timestamp};
Arrays.sort(list);
String sign = DigestUtils.shaHex(StringUtils.join(list));
```
最终signature的值为例子中的sign
备注：接口中提到需要sha1签名的，除了signature参数不参与签名，其他参数都需要参与外，另外还需要加上应用的secret，签名结果即为signature参数的值

postMan签名方式：
在Pre-request Scripts中添加如下代码即可：
```javascript
var appId = "xxxxx",secret = "xxxx";
var data = {"appId":appId,"nonceStr":Math.random().toString(36).substr(2),"timestamp":Date.now()};
var list = [];
for(var k in data){
    list.push(data[k]);
}
list.push(secret);
list.sort();
data["signature"] = CryptoJS.SHA1(list.join("")).toString();
postman.setEnvironmentVariable("shenpidata",JSON.stringify(data));
```
在request body raw中加入以下内容，并将内容设置为application/json：
```
{{shenpidata}}
```

##1、获取团队accessToken,该接口为企业自建应用获取团队accessToken
- url: /gateway/oauth2/token/getTeamAccessToken
- 参数：
```
{appId:xxxx,nonceStr:xxxx,signature:xxxx,timestamp:xxxx}
备注：signature由sha1签名算法得到，该接口为企业自建应用获取团队accessToken
```
- 返回值：
``` 
{
    "data": {
		accessToken:accessToken,
		expireIn:有效时间(秒),
		refreshToken:token刷新令牌
    },
    "errorCode": 0,
    "success": true
}

示例：
{
    "data": {
        "accessToken": "kuIpBlqyskChznZyJZq4A2ulCPVm2QPt",
        "expireIn": 7200,
        "refreshToken": "goaUXEEKYqzPzoSlXWTY03wrkx6FM9mQ"
    },
    "errorCode": 0,
    "success": true
}
```
## 2、刷新accessToken
- url: /gateway/oauth2/token/refreshToken
- 参数：
```
{appId:xxxx,refreshToken:xxx,nonceStr:xxxx,signature:xxxx,timestamp:xxxx}
备注：signature由sha1签名算法得到
```
- 返回值：
``` 
{
        "data": {
    		accessToken:accessToken,
    		expireIn:有效时间(秒),
    		refreshToken:token刷新令牌
        },
        "errorCode": 0,
        "success": true
}
示例：
{
     "data": {
        "accessToken": "kuIpBlqyskChznZyJZq4A2ulCPVm2QPt",
        "expireIn": 7200,
        "refreshToken": "goaUXEEKYqzPzoSlXWTY03wrkx6FM9mQ"
         },
        "errorCode": 0,
        "success": true
}
```

# API接口
## 1、获取轻应用管理员权限内所有报表分类及OpenApi创建的数据表信息
- url: /api/open/linkerp/ReportDataTableapi/getAdminTableInfos
- 参数：
```
{"eid":"企业工作圈信息" }
示例：
{"eid":"6826231"}
```
- 返回值：
``` 
[
        {
            "id": "报表分类ID", 
            "name": "报表分类名称", 
            "tableInfos": [
                {
                    "dataUpdateTime": 数据最后更新时间
                    "id": "数据表ID", 
                    "name": "数据表名称"
                }
            ]
        }
]
示例：
[
           {
            "id": "59ce14c8d026670fe482b534",
            "name": "财务(示例)", 
            "tableInfos": [
                {
                    "dataUpdateTime": 1506678722531, 
                    "id": "59ce1599d02667072b08b9c1",
                    "name": "自定义api导入测试"
                }
            ]
        },
        {
            "id": "59ce14c8d026670fe482b532",
            "name": "销售(示例)",
            "tableInfos": [
                {
                    "dataUpdateTime": 1506678722531,
                    "id": "59ce1599d02667072b08b9c2",
                    "name": "自定义api导入测试1"
                }
            ]
        }
]
```

## 2、创建数据表头
- url: /api/open/linkerp/customizedapi/createTableInfoHeader
- 参数：
```
{
        "eid": "企业工作圈信息",    
        "name": "数据表名称",  
        "reportCategoryId": "报表分类ID", 
        "headerItems": [
            {
                "name": "表头栏位名称",  
                "dataType": "表头栏位类型"  // 目前支持string(字符串),number(数字),timestamp(时间)
            }
        ]
}
示例：
{
        "eid": "6826231",
        "name": "自定义API导入测试",
        "reportCategoryId": "59cdae43d5ad7912a47b968a",
        "headerItems": [
            {
                "name": "表头1",
                "dataType": "string" 
            },
            {
                "name": "表头2",
                "dataType": "number"
            },
            {
                "name": "表头3",
                "dataType": "timestamp"
            }
        ]
}
```
- 返回值：
``` 
{"tableInfoId":"新建数据表ID" }
示例：
{"tableInfoId":"59cdbbf4d5ad790605e1ba4f"}
```

## 3、上传数据表数据
- url: /api/open/linkerp/customizedapi/saveTableInfoData
- 参数：

```
{
    "eid": "企业工作圈信息",   
    "batchId": "批次ID",  //每个批次不同的唯一ID，用于数据上传状态查询
    "reportCategoryId": "报表分类ID",   
    "tableInfoId": "数据表ID",   
    "addType": "数据保存类型",//1：追加(在原数据上增加新数据)，2：替换(删除掉数据表中所有数据，添加新数据)
    "data": [
        {
            "表头栏位名称": "数据",//表头数据必须与表头栏位名称一一对应，
            且与定义的数据类型相符
        }
    ]
}

示例：
{
    "eid": "6826231", 
    "batchId": "5934b509570f227420998d3b",  
    "reportCategoryId": "59cdae43d5ad7912a47b968a",
    "tableInfoId": "59cdbbf4d5ad790605e1ba4f",
    "addType": 0,
    "data": [
        {
            "表头1": "中国",
            "表头2": "100",
            "表头3": "2008-08-08 20:00:00"
        },
        {
            "表头1": "美国",
            "表头2": "80"",
            "表头3": "2009-08-07 20:00:00"
        },
        {
            "表头1": "法国",
            "表头2": "60"",
            "表头3": "2011-07-08 20:00:00"
        }
    ]
}
```
- 返回值：
``` 
{
        "errorCode": 错误码, 
        "success": 接口调用结果  //true,false
}
示例：
{
        "errorCode": 0, 
        "success": true  
}
```
- 说明：
1.每次上传数据最大数量为500条数据
2.返回结果success为true只说明数据上传接口调用成功，不代表数据已上传成功。数据上传以异步方式进行，需要使用batchId查询上传数据状态

## 4、查询上传数据表数据上传结果
- url: /api/open/linkerp/customizedapi/getTableInfoDataSaveStatus
- 参数：
```
{
        "eid": "企业工作圈信息",
        "batchId": "批次ID" 
}
示例：
{
        "eid": "6826231",
        "batchId": "5934b509570f227420998d3c"
}
```
- 返回值：
``` 
{
        "error": "错误信息", 
        "status": 执行状态  //0：执行中,1：执行成功,2：执行异常
}
示例：
{
        "error": "",
        "status": 1
}
```
---