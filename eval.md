# 评测

上传文本和音频，获取评测结果

## 接口详情
---
### 请求方法
```
POST
```
### 请求URL
```
http://edu.hivoice.cn:8085/eval/{audioFormat}
```
#### Path Parameters

* **audioFormat** (required)
> 上传的音频格式。 16k signed 16bit pcm；或，连续的opus帧，每帧有两个字节的小端头，每个opus帧由640 bytes pcm编码得到；或窄带amr Example: ```opus```.

  > 可选值:  ```pcm``` , ```opus``` , ```amrnb``` .

#### HTTP header attribute


* **session-id**
> session id，表示该次评测的唯一表示，使用 uuid，session id 必须要在appkey前面，顺序不可颠倒


* **appkey** (required)
> app key


* **score-coefficient**
> 分数调整定制参数，可以对同样质量的语音调整得分高低，范围是0.6~1.9，默认情况下是1.0，设置越低，打分越严格，设置系数越高，打分越松，一般小学生设置系数偏高，采取鼓励模式


* **device-id**
> 设备或用户的id标识。一个客户应该保证每个用户拥有唯一的id，建议上传，方便用户的数据上传



#### Form Data

* **text** (required)
> 需要评测的文本

* **mode**
> 评测模式（包含A B C D E G H，A B D H 是常用模式）

>> A：最简单模式，结果只有单词打分没有音素信息

>> B：有音素信息，但是没有音素打分

>> C：跟A一样，区别是最外层有Score总分

>> D：在B的基础上，有音素打分

>> E：返回words字段里的值，有空格和标点符号，但是没有音素打分(针对个别客户需求)

>> G：跟D一样

>> H：选择题打分模式


* **voice**

> 语音数据，```multipart```

###  Examples

#### curl

```
curl -X POST -H "session-id:uuidgen" -H "appkey:www"  -H "Content-Length" -H "device-id:userid" --form text='good' --form mode="A" --form voice=@./good.opus "http://edu.hivoice.cn:8085/eval/opus"
```
#### Java
```
private void httpTest() {
		new Thread() {
			@Override
			public void run() {
				super.run();
				HttpClient httpclient = new DefaultHttpClient();
				HttpPost httpPost = new HttpPost("http://edu.hivoice.cn:8085/eval/pcm");
				MultipartEntity customMultiPartEntity = new MultipartEntity();
				try {
					HttpResponse response = null;
					JSONObject json = new JSONObject();
					json.put("mode", "A");
					json.put("text", "hello, i am nice to meet you");
					customMultiPartEntity.addPart("mode", new StringBody("A", Charset.forName("UTF-8")));
					customMultiPartEntity.addPart("text", new StringBody("hello, i am nice to meet you", Charset.forName("UTF-8")));
					String file_str = "/storage/emulated/0/KAR/test12320161123T104035.pcm";
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
#### response headers
```
Content-Type:application/json
```
#### response body
```
{
  "lines": [
    {
      "begin": 0,
      "end": 2.21,
      "fluency": 66.061,
      "integrity": 100,
      "pronunciation": 85.984,
      "sample": "good",
      "score": 84.988,
      "usertext": "good",
      "words": [
        {
          "begin": 0.08,
          "end": 1.21,
          "score": 4.139,
          "text": "sil",
          "type": 4,
          "volume": 1
        },
        {
          "StressOfWord": 1,
          "begin": 1.21,
          "end": 1.76,
          "score": 8.598,
          "text": "good",
          "type": 2,
          "volume": 3.755
        },
        {
          "begin": 1.76,
          "end": 2.21,
          "score": 3.576,
          "text": "sil",
          "type": 4,
          "volume": 1
        }
      ]
    }
  ],
  "version": "full 1.0"
}
```
