# CardOCR
**证件识别**是一个apk插件，为应用开发方提供身份证、驾驶证、车牌号、护照等的OCR识别服务，如通过扫描身份证，可以快速识别二代身份证上的身份证号等信息，并将这些信息返回给第三方应用。

最新版本的OCR识别库具备以下明显优势：

1. 识别率大幅提高，车牌号和身份证正常情况下毫秒级完成识别，成功率达到99%以上。
1. 车牌识别除了支持普通的蓝色、黄色、警用等车牌外，同时也支持新能源车牌。
1. 驾驶证识别率大幅提升，证件识别实现自动对焦锁定，用户体验大幅提升。

- ### **[体验下载](https://gitee.com/jarlen/card-ocrad/blob/main/OCR_client_V1.240902_enc.apk)**

- ### **[商务洽谈](https://gitee.com/jarlen/card-ocrad/blob/main/qrcode.PNG)**

![image](https://github.com/user-attachments/assets/f24cfb76-e1a5-48ab-98f6-6824ba5a98bf)


# 使用指南

## 接口简介

证件识别APK为通过Activity意图调用的方式为业务应用提供服务。在调用接口方法前，务必保证识别库插件已经安装。

> 插件可以提示用户手动安装，也可由业务应用方内置，出发安装。插件的包名是：**cn.lvzhulin.zjsb**。

组件所提供的接口调用方法都是通过隐试启动activity并结合onActivityResult方法回调实现结果的回传来实现的，

**【示例代码】**

```java
Intent intent = new Intent(“Action 名称，下面的接口详细调用中会阐述”);
Intent.putExtra(“pkgName”, “第三方应用（调用方）的包名”);
startActivityForResult(intent, REQUEST_CODE);
```

另外，API接口回传的数据结构有多种，包括直接返回部分数据信息，以及将所有识别的结果转换为json字符串后返回，调用方可以结合业务场景灵活选择处理。

## 接口描述
插件根据业务场景的不通提供两种调用方式，第一种是直接通过相机拍照识别的调用，另一种是通过扫描本地已经存在的图片来完成识别，调用方可以根据业务场景需求灵活选择。

### 身份证识别
Activity启动意图Action定义为： **cn.lvzhulin.zjsb.sfzsb.action**

**【示例代码】**

请求启动：

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.sfzsb.action");
Intent.putExtra(“pkgName”, “com.jarlen.app”);
/*身份证识别标识，
*传0或者不传表示是识别正面，
*传1表示识别反面*/
Intent.putExtra(“sfzbs”,1);
startActivityForResult(intent, 11);
```

结果返回：

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode != RESULT_OK) {
        return;
    }
    if (requestCode == 11) {
        /*获取身份证号*/k
        String sfzh = data.getStringExtra("sfzh");
        /*json字符串包括姓名、性别、年龄、名族、身份证号、居住地址等所有信息*/
        String json = data.getStringExtra("json");
    }
}
```

| 字段                                | 描述                     |
| ----------------------------------- | ------------------------ |
| xm                                  | 姓名                     |
| xb                                  | 性别                     |
| mz                                  | 名族                     |
| sfzh                                | 身份证号                 |
| csrq                                | 出生日期                 |
| csdz                                | 出生地址                 |
| blzd                                | 保留字段                 |
| ----------------------------------- | 以下是身份证背面识别数据 |
| qfjg                                | 签发机关                 |
| yxqx                                | 有效期限                 |
| qfrq                                | 签发日期                 |
| yxqz                                | 有效期至                 |

### 车牌识别
Activity启动意图Action定义为： **cn.lvzhulin.zjsb.cpsb.action**

**【示例代码】**

请求启动

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.cpsb.action");
Intent.putExtra(“pkgName”, “包名”);
startActivityForResult(intent, 11);
```

结果返回

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode != RESULT_OK) {
        return;
    }
    if (requestCode == 11) {
        String number = data.getStringExtra("number"); 
        String color = data.getStringExtra("color");
    }
}
```

| 字段   |   描述   |
| ------ | :------: |
| number |  车牌号  |
| color  | 车牌颜色 |

### 驾驶证识别
Activity启动意图Action定义为： **cn.lvzhulin.zjsb.jszsb.action**

**【示例代码】**

请求启动

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.jszsb.action");
Intent.putExtra(“pkgName”, “包名”);
startActivityForResult(intent, 11);
```

结果返回

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode != RESULT_OK) {
        return;
    }
    if (requestCode == 11) {
        /*包括姓名、性别、身份证号等所有信息*/
        String json = data.getStringExtra("json");
    }
}
```

> 结果json包括姓名、性别、身份证号等所有信息，通过JSONObject或转为Bean获取，主要字段参数描述如下：

| 字段    | 描述           |
| ------- | -------------- |
| xm      | 姓名           |
| xb      | 性别           |
| sfzh    | 身份证号       |
| csrq    | 出生日期       |
| csdz    | 出生地址       |
| cslzrq  | 初始领证日期   |
| zjcx    | 准驾车型       |
| yxqqsrq | 有效期起始日期 |
| yxjzrq  | 有效截止日期   |
| yxqx    | 有效期限       |
| blzd    | 保留字段       |

### 护照识别
Activity启动意图Action定义为： **cn.lvzhulin.zjsb.passport.action**

**【示例代码】**

请求启动

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.passport.action");
Intent.putExtra(“pkgName”, “包名”);
startActivityForResult(intent, 11);
```

结果返回

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode != RESULT_OK) {
        return;
    }
    if (requestCode == 11) {
        /*获取所有信息*/
        String json = data.getStringExtra("data");
    }
}
```

> 结果json包括姓名、性别、身份证号等所有信息，通过JSONObject或转为Bean获取，主要字段参数描述如下：

| 字段     | 描述           |
| -------- | -------------- |
| hzlx     | 护照类型       |
| hzhmmrz  | 护照号码MRZ    |
| bgxm     | 本国姓名       |
| ywxm     | 英文姓名       |
| xb       | 性别           |
| csrq     | 出生日期       |
| yxqz     | 有效期至       |
| qfgdm    | 签发国代码     |
| ywx      | 英文姓         |
| ywm      | 英文名         |
| mrz1     | MRZ1           |
| mrz2     | MRZ2           |
| czrgjdm  | 持证人国籍代码 |
| hzhm     | 护照号码       |
| csdd     | 出生地点       |
| qfdd     | 签发地点       |
| qfrq     | 签发日期       |
| rfid_mrz | RFID MRZ       |
| ocr_mrz  | OCR MRZ        |
| csddpy   | 出生地点拼音   |

### 浏览本地图片识别

Activity启动意图Action定义为： **cn.lvzhulin.zjsb.picture.action**

【代码示例】

```java
Intent intent = new Intent(“cn.lvzhulin.zjsb.picture.action”); //action名称
intent.putExtra(“pkgName”, “调用方的包名”);
intent.putExtra(“picture_type”, type);//type是业务类型，下面会定义
intent.putExtra(“path”, filepath);//path是图片的存储路径，包括文件名
startActivityForResult(intent, REQUEST_CODE);
```
其中type定义如下：
| type类型名称 | 传入值 |
|----------|-----|
| 识别车牌     | 1   |
| 识别身份证    | 2   |
| 识别驾照     | 3   |
| 识别护照     | 4   |

通过onActivityForResult获取，通过intent.getIntExtra(“type”)获取业务类型，然后根据以上每一个业务定义的返回结果标识来获取结果，

例如：调用接口传入本地的车牌号图片路径识别车牌号：

```java
Intent intent = new Intent(“cn.lvzhulin.zjsb.picture.action”); 
intent.putExtra(“pkgName”, “调用方的包名”);
intent.putExtra(“picture_type”, 1); //1标识识别车牌
intent.putExtra(“path”, “图片路径”);//车牌号图片存储路径
startActivityForResult(intent, REQUEST_CODE);
```
结果返回：

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode != RESULT_OK) {
        return;
    }
    if (requestCode == 11) {
        int type = data.getIntExtra("type", 0);
        if (type == 0) { //type是0表示识别失败
            /*识别失败*/
        } else if (type == 1) { //type是1表示识别车牌
            String number = data.getStringExtra("number"); //获取车牌号信息
            String color = data.getStringExtra("color");//获取车牌颜色
        }
    }
}
```
数据获取成功后，可以参考【车牌识别】结果来处理；
**注意！**如果获取到的type值是0，标识识别失败！

# 使用授权

## 授权信息

使用前，需提供调用方信息，具体如下：

| 类别           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| 调用方应用签名 | 可通过命令'keytool -printcert -jarfile xxx.apk'或其他工具读取 |
| 应用包名       | 与build.gradle中配置的applicationId一致                      |

## 费用说明

| 类别   | 描述     |
| ------ | -------- |
| 按应用 | 99元/年  |
| 按签名 | 298元/年 |
| 终身   | 698元/年 |

### 定制服务

| 类别     | 描述                                 |
| -------- | ------------------------------------ |
| 定制UI   | 按照定制UI需求范围，评估投入进行收费 |
| 新增功能 | 按照定制需求范围，评估投入进行收费   |
