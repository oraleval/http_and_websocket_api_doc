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

* **appkey** (required)
> app key

* **session-id**
> session id，如果需要在评测完成以后播放改该录音，则需要生成uuid

* **score-coefficient**
> 分数调整定制参数，可以对同样质量的语音调整得分高低，具体取值咨询客户经理

#### Form Data

* **text** (required)
> 需要评测的文本

* **mode**
> 评测模式

* **voice**
> 语音数据，```multipart```

### CURL Example
```
curl --include \
     --request POST \
     --header "Content-Type: multipart/form-data, boundary=----------------------34c6303dbc77413e" \
     --header "score-coefficient: <coefficient>" \
     --header "appkey: <custom app key>" \
     --header "session-id: <random-generated-uuid>" \
     --data-binary "------------------------34c6303dbc77413e
Content-Disposition: form-data; name=\"text\"
hello, i am nice to meet you
------------------------34c6303dbc77413e
Content-Disposition: form-data; name=\"mode\"
A
------------------------34c6303dbc77413e
Content-Disposition: form-data; name=\"voice\"; filename=\"pcm.raw\"
Content-Type: application/octet-stream
<audio binary data>
------------------------34c6303dbc77413e--" \
'http://edu.hivoice.cn:8085/eval/audioFormat'
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
  "version": "full 1.0",
  "lines": [
    {
      "sample": "hello i am nice to meet you",
      "usertext": "hello i am nice to meet you",
      "begin": 0,
      "end": 2.39,
      "score": 78.932,
      "words": [
        {
          "text": "hello",
          "type": 2,
          "begin": 0.08,
          "end": 0.2,
          "volume": 5.647,
          "score": 0.459,
          "subwords": [
            {
              "subtext": "HH",
              "volume": 6.333,
              "tone": [
                0,
                0,
                0
              ],
              "begin": 0.08,
              "end": 0.11
            },
            {
              "subtext": "EH",
              "volume": 5.139,
              "tone": [
                0,
                0,
                0
              ],
              "begin": 0.11,
              "end": 0.14
            },
            {
              "subtext": "L",
              "volume": 4.782,
              "tone": [
                0,
                0,
                0
              ],
              "begin": 0.14,
              "end": 0.17
            },
            {
              "subtext": "OW",
              "volume": 6.333,
              "tone": [
                0,
                0,
                0
              ],
              "begin": 0.17,
              "end": 0.2
            }
          ]
        },
        {
          "text": "sil",
          "type": 4,
          "begin": 0.2,
          "end": 0.76,
          "volume": 3.991,
          "score": 9.332,
          "subwords": [
            {
              "subtext": "sil",
              "volume": 3.991,
              "tone": [
                0,
                0,
                0
              ],
              "begin": 0.2,
              "end": 0.76
            }
          ]
        },
        {
          "text": "i",
          "type": 2,
          "begin": 0.76,
          "end": 1.1,
          "volume": 9,
          "score": 9.094,
          "subwords": [
            {
              "subtext": "AY",
              "volume": 9,
              "tone": [
                146.394,
                126.605,
                123.192
              ],
              "begin": 0.76,
              "end": 1.1
            }
          ]
        },
        {
          "text": "sil",
          "type": 4,
          "begin": 1.1,
          "end": 1.31,
          "volume": 3.727,
          "score": 9.248,
          "subwords": [
            {
              "subtext": "sil",
              "volume": 3.727,
              "tone": [
                0,
                0,
                0
              ],
              "begin": 1.1,
              "end": 1.31
            }
          ]
        },
        {
          "text": "am",
          "type": 2,
          "begin": 1.31,
          "end": 1.49,
          "volume": 9,
          "score": 8.562,
          "subwords": [
            {
              "subtext": "AE",
              "volume": 9,
              "tone": [
                119.192,
                116.844,
                114.825
              ],
              "begin": 1.31,
              "end": 1.44
            },
            {
              "subtext": "M",
              "volume": 9,
              "tone": [
                113.335,
                113.166,
                113.654
              ],
              "begin": 1.44,
              "end": 1.49
            }
          ]
        },
        {
          "text": "nice",
          "type": 2,
          "begin": 1.49,
          "end": 1.77,
          "volume": 8.516,
          "score": 8.176,
          "subwords": [
            {
              "subtext": "N",
              "volume": 9,
              "tone": [
                115.2,
                114.866,
                115.689
              ],
              "begin": 1.49,
              "end": 1.61
            },
            {
              "subtext": "AY",
              "volume": 8.959,
              "tone": [
                117.216,
                113.601,
                110.524
              ],
              "begin": 1.61,
              "end": 1.67
            },
            {
              "subtext": "S",
              "volume": 7.588,
              "tone": [
                0,
                0,
                0
              ],
              "begin": 1.67,
              "end": 1.77
            }
          ]
        },
        {
          "text": "to",
          "type": 2,
          "begin": 1.77,
          "end": 1.88,
          "volume": 8.803,
          "score": 8.894,
          "subwords": [
            {
              "subtext": "T",
              "volume": 8.605,
              "tone": [
                146.014,
                141.925,
                130.544
              ],
              "begin": 1.77,
              "end": 1.82
            },
            {
              "subtext": "UW",
              "volume": 9,
              "tone": [
                127.32,
                128.441,
                129.684
              ],
              "begin": 1.82,
              "end": 1.88
            }
          ]
        },
        {
          "text": "meet",
          "type": 2,
          "begin": 1.88,
          "end": 2.17,
          "volume": 8.17,
          "score": 9.183,
          "subwords": [
            {
              "subtext": "M",
              "volume": 9,
              "tone": [
                5.074,
                140.056,
                146.313
              ],
              "begin": 1.88,
              "end": 1.96
            },
            {
              "subtext": "IY",
              "volume": 9,
              "tone": [
                152.391,
                148.75,
                147.379
              ],
              "begin": 1.96,
              "end": 2.05
            },
            {
              "subtext": "T",
              "volume": 6.511,
              "tone": [
                0,
                138.896,
                123.396
              ],
              "begin": 2.05,
              "end": 2.17
            }
          ]
        },
        {
          "text": "you",
          "type": 2,
          "begin": 2.17,
          "end": 2.34,
          "volume": 7.942,
          "score": 9.227,
          "subwords": [
            {
              "subtext": "Y",
              "volume": 9,
              "tone": [
                109.662,
                105.525,
                103.447
              ],
              "begin": 2.17,
              "end": 2.22
            },
            {
              "subtext": "UW",
              "volume": 6.884,
              "tone": [
                109.658,
                113.829,
                113.332
              ],
              "begin": 2.22,
              "end": 2.34
            }
          ]
        },
        {
          "text": "sil",
          "type": 4,
          "begin": 2.34,
          "end": 2.39,
          "volume": 4.735,
          "score": 8.538,
          "subwords": [
            {
              "subtext": "sil",
              "volume": 4.735,
              "tone": [
                0,
                0,
                0
              ],
              "begin": 2.34,
              "end": 2.39
            }
          ]
        }
      ]
    }
  ]
}
```
