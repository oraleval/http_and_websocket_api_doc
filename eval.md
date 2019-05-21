# 云知声口语评测服务HTTP API文档

* [1.概要](#outline)
* [2.HTTP备份流程](#backup)
* [3.HTTP接口调用流程](#process)
* [4.HTTP接口拼接audio url](#audio)

## <a name="outline"></a>1. 概要

> 本文档主要描述HTTP接口的备份流程和接口调用方法，备份流程主要是用于提高评测成功率，凡是使用HTTP方式接入评测，都建议使用备份流程

## <a name="backup"></a>2. HTTP备份流程

>> ![image](https://github.com/oraleval/http_api_doc/blob/master/HTTP%E8%AF%84%E6%B5%8B%E5%A4%87%E4%BB%BD%E6%B5%81%E7%A8%8B.png)

>> 备注：服务端未设置超时时间，客户端每步的超时时间算式为：

>> a.如果文本 <=10 个单词，设置为 3 秒；

>> b.如果文本 >10 个单词，设置为 3 + (n-10)/5 秒；
   
## <a name="process"></a>3. HTTP接口调用流程

上传文本和音频，获取评测结果


---
### 3.1 请求方法
```
POST
```
### 3.2 请求URL

```
英语评测 
> 普通http评测访问： http://edu.hivoice.cn:8085/eval/{audioFormat}   端口：8085
> 小程序等https请求访问：https://edu.hivoice.cn/eval/{audioFormat}   默认端口：443

中文评测
> 普通http评测访问：http://cn-edu.hivoice.cn/eval/{audioFormat}   端口：80
> 小程序等https请求访问：https://cn-edu.hivoice.cn/eval/{audioFormat}   默认端口：443
 
> 备 注：请求的URL需跟上传的音频格式对应，例如amr格式的音频对应英语评测地址为 http://edu.hivoice.cn:8085/eval/amrnb
> 可选值：mp3/silk/opus/amrnb/wxspeex
```

### 3.3 Path Parameters

* **audioFormat** (required)
> 上传的音频格式。 连续的opus帧，每帧有两个字节的小端头，每个opus帧由640 bytes pcm编码得到；或窄带amr Example: ```opus```.
> 音频采用 8K/16K采样率 16Bit编码方式生成

  >> {audioFormat}可选值:  ```mp3``` , ```silk``` , ```opus``` , ```amrnb``` ,```wxspeex```. mp3音频中不能含有tag信息，所谓Tag 信息，就是在MP3文件中加入曲名、演唱者、专集、年月、流派、注释等信息，**不支持双声道音频**
  
  >> 对于mp3，一般是建议采样率设置为16K，比特率设置为64 kbps

  >> 对于silk 格式，微信编译器上的生成的文件是base64编码的，不是silk格式，评测时会报错，需要使用真机生成的silk文件。
  
  >> 从2019年4月12日开始支持微信公众号的speex格式（16K），请求访问地址是http://edu.hivoice.cn:8085/eval/wxspeex


### 3.4 HTTP header attribute


* **session-id** (required)
> session id，表示该次评测的唯一表示，必须上传，使用 uuid。


* **appkey** (required)
> app key
> 需要申请


* **score-coefficient**
> 分数调整定制参数，可以对同样质量的语音调整得分高低，范围是0.6~1.9，默认情况下是1.0，设置越低，打分越严格，设置系数越高，打分越松，一般小学生设置系数偏高，采取鼓励模式


* **device-id**
> 设备或用户的id标识。一个客户应该保证每个用户拥有唯一的id，必须上传


* **Wrap-Create-Time** (required)
> 值为 ```true``` 添加此请求头， 返回结果将会添加sessionId和和createTime （结构见具体返回）
> 注意：js解析时间精度会丢失，请将时间转字符串后再解析

* **X-EngineType**
> 值为：```oral.zh_CH```
> 这个是中文评测时需要添加的请求头， **仅当使用中文评测**时添加此请求头

### 3.5 Form Data

**需要按照text、mode、voice顺序依次传入，不可乱序**

* **text** (required)
> 需要评测的文本 **string格式**


* **mode**
> 评测模式（包含A B C D E G H，A B G H 是常用模式）

>> A：最简单模式，结果只有单词打分没有音素信息

>> B：有音素信息，但是没有音素打分

>> C：跟A一样，同时适用于篇章打分

>> D：在B的基础上，有音素打分

>> E：返回words字段里的值，有空格和标点符号，也包含音素打分

>> G：跟D一样

>> H：选择题打分模式


* **voice**

> 语音数据，```multipart```

### 3.6 Examples

#### curl

```bash
#ZH-Oral
curl -X POST -H "session-id:`uuidgen`" -H "appkey:www"  -H "Content-Length" -H "device-id:userid" -H "X-EngineType:oral.zh_CH" --form text="学而时习之，不亦说乎。 --form mode="B" --form voice=@'test.opus' "http://cn-edu.hivoice.cn/eval/opus"

#EN-Oral
curl -X POST -H "session-id:`uuidgen`" -H "appkey:www"  -H "Content-Length" -H "device-id:userid" --form text="good" --form mode="A" --form voice=@'good.opus' "http://edu.hivoice.cn:8085/eval/opus"

```
#### Java
```Java
private void httpTest() {
		new Thread() {
			@Override
			public void run() {
				super.run();
				HttpClient httpclient = new DefaultHttpClient();
				HttpPost httpPost = new HttpPost("http://edu.hivoice.cn:8085/eval/mp3");
				MultipartEntity customMultiPartEntity = new MultipartEntity();
				try {
					HttpResponse response = null;
					JSONObject json = new JSONObject();
					json.put("mode", "A");
					json.put("text", "hello, i am nice to meet you");
					customMultiPartEntity.addPart("mode", new StringBody("A", Charset.forName("UTF-8")));
					customMultiPartEntity.addPart("text", new StringBody("hello, i am nice to meet you", Charset.forName("UTF-8")));
					String file_str = "/storage/emulated/0/KAR/test12320161123T104035.mp3";
					ContentBody fileBody = new FileBody(new File(file_str));
					customMultiPartEntity.addPart("voice", fileBody);
					httpPost.setEntity(customMultiPartEntity);
					httpPost.setHeader("appkey", "testAppKey");
					String uuid_str = UUID.randomUUID().toString();
					httpPost.setHeader("session-id", uuid_str);
					httpPost.setHeader("device-id", "userid");
					response = httpclient.execute(httpPost);
					if (response != null && response.getStatusLine().getStatusCode() == 200) {
						// file.setLastModified(System.currentTimeMillis());
						String text = EntityUtils.toString(response.getEntity(), HTTP.UTF_8);
						//JSONObject jsonObject = new JSONObject(text);
						Log.e("response",text);
					}
				} catch (Exception e) {
					e.printStackTrace();
				}
				httpclient.getConnectionManager().shutdown();
			}
		}.start();
	}
```
### response

#### HTTP status
```
200
```

## <a name="audio"></a>4. HTTP拼接audio url

> 如果使用http协议，且想要获取用户录音结果，则可以根据响应请求结果中的header进行拼接音频url，获取音频

> 一个正常的http请求，在返回结果的header中有session-id字段，例如：

> **session-id →sh:1542272221203805792:02055555-f4cd-4fef-8ed8-1a2089056acf**

> 其中，sh是代表地域名称area，1542272221203805792是代表createtime；02055555-f4cd-4fef-8ed8-1a2089056acf 是代表请求中传入的session-id

> 最后可以根据以上三个信息拼接url，固定字段http://edu.hivoice.cn:9088/WebAudio-1.0-SNAPSHOT/audio/play/{session-id}/{createtime}/{area}

> 以上示例，最后拼接的url是http://edu.hivoice.cn:9088/WebAudio-1.0-SNAPSHOT/audio/play/02055555-f4cd-4fef-8ed8-1a2089056acf/1542272221203805792/sh

**错误码参照：**<a href=https://github.com/oraleval/ErrorCodeList/wiki/HomePage>错误码说明</a>

**Json字段说明请查找：**<a href=https://github.com/oraleval/FAQ-Docs/blob/master/Json%20Description.md>Json字段说明</a>
#### response headers
```
Content-Type:application/json
```
#### response body
```Json
{
  "version": "full 1.0",
  "score": 88.66,
  "EvalType": "general",
  "lines": [
    {
      "sample": "smart",
      "usertext": "smart",
      "begin": 0,
      "end": 1.321,
      "score": 88.66,
      "fluency": 91.868,
      "integrity": 100,
      "pronunciation": 88.561,
      "words": [
        {
          "StressOfWord": 1,
          "phonetic": "smɑːt",
          "text": "smart",
          "type": 2,
          "begin": 0.411,
          "end": 1.301,
          "volume": 7.479,
          "score": 8.995,
          "subwords": [
            {
              "subtext": "s",
              "volume": 7.397,
              "begin": 0.411,
              "end": 0.541,
              "score": 8.355
            },
            {
              "subtext": "m",
              "volume": 9,
              "begin": 0.541,
              "end": 0.681,
              "score": 9.798
            },
            {
              "subtext": "ɑː",
              "volume": 6.492,
              "begin": 0.681,
              "end": 0.921,
              "score": 9.425
            },
            {
              "subtext": "t",
              "volume": 7.025,
              "begin": 0.921,
              "end": 1.301,
              "score": 8.937
            }
          ]
        }
      ]
    }
  ]
}
```
