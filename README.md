# CardOCR

**证件识别**为应用开发方提供身份证、驾驶证、行驶证、车牌号、护照、签证等的OCR识别服务；例如，通过扫描身份证，可以快速识别二代身份证上的身份证号等信息，并将这些信息返回给第三方应用。

最新版本的OCR识别库具备以下明显优势：

1. 识别不需要连接网络，即可实现离线识别。
1. 识别率大幅提高，光线正常情况下毫秒级完成识别，成功率达到99%以上。
1. 车牌识别除了支持普通的蓝色、黄色、警用等车牌外，同时也支持新能源车牌。
1. 驾驶证识别率大幅提升，证件识别实现自动对焦锁定，用户体验大幅提升。
1. 支持的证件丰富，目前支持身份证、车牌、机动车驾驶证、机动车行驶证、护照、签证。
1. 支持插件安装与SDK集成方式，随意可选。

- ### **[体验下载](https://raw.githubusercontent.com/jarlen/CardOCR/main/CardOCR_enc.apk)**

![image](https://gitee.com/jarlen/card-ocrad/raw/main/qrcode_apk.jpg)

- ### **[商务洽谈](https://gitee.com/jarlen/card-ocrad/raw/main/qrcode_me.jpg)**

![image](https://gitee.com/jarlen/card-ocrad/raw/main/qrcode_me.jpg)


# 使用指南

**证件识别**以安装APK插件或集成Sdk的形式，为应用调用方提供证件识别服务。

其中，APK插件的方式需要在调用接口方法前，务必保证识别库插件已经安装；而SDK的方式，需要在代码工程中引用相关SDK库。

> 【插件方式】可以提示用户手动安装，也可由业务应用方内置，触发提示用户安装。插件的包名是：**cn.lvzhulin.zjsb**。
>
> 【SDK引用方式】将**OCR-Sdk-1.5.aar**放入工程Module中的libs文件里，然后在build.grade文件依赖项配置如：`implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')`，另外，获取授权文件ocr.lic后，放入工程中assets直属文件夹下。
>

**调用方式**

应用使用方通过隐试启动activity的方式启动识别服务，然后结合onActivityResult回调方法获取识别并解析结果。

* 启动识别服务

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.ocr");
intent.putExtra("pkgName","第三方应用（调用方）的包名");
intent.putExtra("ocrType","业务标识，例如:sfz");
startActivityForResult(intent, REQUEST_CODE);
```

> Intent的**action**定义规则为：**识别服务提供方应用包名.ocr**；例如，APK插件方式的身份证识别action定义为：**cn.lvzhulin.zjsb.ocr**，"**cn.lvzhulin.zjsb**"为APK插件的应用包名；SDK集成方式的action定义为 **集成方应用包名.ocr**
>
> **ocrType**为业务标识，具体见各个业务说明。

* 获取识别结果

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode != Activity.RESULT_OK) {
        return;
    }
    String jsonResult = data.getStringExtra("result_json");
}
```

> 识别结果被封装为JSON的形式。由于各个业务识别内容区别，调用方可通过识别内容结合字段获取对应的参数值。

## 接口描述
插件根据业务场景的不通提供两种调用方式，第一种是直接通过相机拍照识别的调用，另一种是通过扫描本地已经存在的图片来完成识别，调用方可以根据业务场景需求灵活选择。下面以APK插件方式进行接口定义描述。

### 身份证识别
Activity启动意图参数**ocrType**定义为： **sfz**

**【示例代码】**

请求启动：

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.ocr");
intent.putExtra("pkgName","com.jarlen.app");
intent.putExtra("ocrType","sfz");
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
    /*
    *json字符串包括姓名、性别、年龄、民族、出生、住址、公民身份号码等正面信息
    *以及签发机关、有效期限、签发日期、有效期至等反面信息*/
    String jsonResult = data.getStringExtra("result_json");
}
```

### 护照识别

Activity启动意图参数**ocrType**定义为： **passport**

**【示例代码】**

请求启动

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.ocr");
intent.putExtra("pkgName","com.jarlen.app");
intent.putExtra("ocrType","passport");
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
     /*
    *json字符串包括姓名、性别、住址、出生日期、证号、初始领证日期、准驾车型、
    *有效起始日期、有效期限、有效截止日期等信息*/
    String jsonResult = data.getStringExtra("result_json");
}
```

### 签证识别

Activity启动意图参数**ocrType**定义为： **visa**

**【示例代码】**

请求启动

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.ocr");
intent.putExtra("pkgName","com.jarlen.app");
intent.putExtra("ocrType","visa");
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
     /*
    *json字符串包括证件类型、本国姓名、英文姓名、性别、出生日期、有效期至、签发国代码、
    *英文姓、英文名、MRZ1、MRZ2、持证人国籍代码、证件号码、护照号码/通行证号码、签发地点、
    *签发日期、来往次数、出访日期、离境日期、停留天数、签证种类等信息*/
    String jsonResult = data.getStringExtra("result_json");
}
```

### 车牌识别

Activity启动意图参数**ocrType**定义为： **cp**

**【示例代码】**

请求启动

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.ocr");
intent.putExtra("pkgName","com.jarlen.app");
intent.putExtra("ocrType","cp");
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
    /*
    *json字符串包括号牌号码、车辆颜色*/
    String jsonResult = data.getStringExtra("result_json");
}
```

### 驾驶证识别
Activity启动意图参数**ocrType**定义为：**driving**

**【示例代码】**

请求启动

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.ocr");
intent.putExtra("pkgName","com.jarlen.app");
intent.putExtra("ocrType","driving");
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
    /*
    *json字符串包括姓名、性别、住址、出生日期、证号、初始领证日期、准驾车型、
    *有效起始日期、有效期限、有效截止日期等信息*/
    String jsonResult = data.getStringExtra("result_json");
}
```

### 行驶证识别

Activity启动意图参数**ocrType**定义为： **vehicle**

**【示例代码】**

请求启动

```java
Intent intent = new Intent("cn.lvzhulin.zjsb.ocr");
intent.putExtra("pkgName","com.jarlen.app");
intent.putExtra("ocrType","vehicle");
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
    /*
    *json字符串包括号牌号码、车辆类型、所有人、住址、使用性质、品牌型号、车辆识别代号、
    *发动机号码、注册日期、发证日期等信息*/
    String jsonResult = data.getStringExtra("result_json");
}
```
# 授权说明

## 授权信息

使用前，需提供调用方信息，具体如下：

| 类别           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| 调用方应用签名 | 可通过命令'keytool -printcert -jarfile xxx.apk'或其他工具读取sha1值<br>亦或提供相关apk由我读取。 |
| 应用包名       | 与build.gradle中配置的applicationId一致                      |
| 授权时间       | 一般按年为单位授权，特殊情况另算                             |

>获取授权后，会生成授权文件ocr.lic，将其放入工程assets文件夹直属目录下即可。

## 费用说明

| 类别       | 描述                                   |
| ---------- | -------------------------------------- |
| 按应用     | APK插件：99元/年<br>SDK集成：198元/年  |
| 按应用签名 | APK插件：299元/年<br>SDK集成：598元/年 |
| 终身       | APK插件：1299元<br>SDK集成：2498元     |

### 定制服务

| 类别     | 描述                                 |
| -------- | ------------------------------------ |
| 定制UI   | 按照定制UI需求范围，评估投入进行收费 |
| 新增功能 | 按照定制需求范围，评估投入进行收费   |

# 附录

## 字段定义

| 字段                          | 字段说明            |
| ----------------------------- | ------------------- |
| name                          | 姓名                |
| sex                           | 性别                |
| nation                        | 民族                |
| birth                         | 出生                |
| address                       | 住址                |
| gmsfhm                        | 公民身份号码        |
| authority                     | 签发机关            |
| valid_period                  | 有效期限            |
| passport_issue_date           | 签发日期            |
| expire_date                   | 有效期至            |
| birth_date                    | 出生日期            |
| first_issue_date              | 初始领证日期        |
| driving_class                 | 准驾车型            |
| effective_start_date          | 有效起始日期        |
| effective_end_date            | 有效截止日期        |
| nationality                   | 国籍                |
| passport_type                 | 护照类型            |
| passport_number_MRZ           | 护照号码MRZ         |
| local_name                    | 本国姓名            |
| english_name                  | 英文姓名            |
| issue_country_code            | 签发国代码          |
| english_surname               | 英文姓              |
| english_given_name            | 英文名              |
| mrz1                          | MRZ1                |
| mrz2                          | MRZ2                |
| holder_nationality_code       | 持证人国籍代码      |
| plate_number                  | 号牌号码            |
| vehicle_type                  | 车辆类型            |
| owner                         | 所有人              |
| model                         | 品牌型号            |
| vehicle_identification_number | 车辆识别代号        |
| engine_number                 | 发动机号码          |
| register_date                 | 注册日期            |
| vehicle_issue_date            | 发证日期            |
| use_character                 | 使用性质            |
| vehicle_color                 | 车辆颜色            |
| full_pic_path                 | 完整图片路径        |
| crop_pic_path                 | 裁剪图片路径        |
| head_pic_path                 | 头像图片路径        |
| pic_source_type               | 图片来源类型        |
| driving_license_number        | 证号                |
| license_type                  | 证件类型            |
| export_number_MRZ             | MRZ导出号码         |
| zjhm                          | 证件号码            |
| passport_number               | 护照号码/通行证号码 |
| issue_place                   | 签发地点            |
| remark                        | 备注                |
| visit_times                   | 来往次数            |
| visit_date                    | 出访日期            |
| leave_date                    | 离境日期            |
| stay_days                     | 停留天数            |
| visa_type                     | 签证种类            |
| extend                        | 保留                |

