# 翻译资源

## 介绍

批改网翻译是通过标准答案与学生作文比较并综合学生作文内容后给出分数。提供的标准答案越多，准确度越高。

如果在实际调用接口时，无法提供足够多的答案，可能会导致那些认真翻译但是因为答案和标准答案相差较大的学生得分偏低。

这个问题可以用我们提供一个参数通过控制作文和翻译的得分比例来优化，翻译得分的默认权重是1，建议将翻译得分权重改为0.3。

## 句子翻译(异步)
> API: http://api.pigai.org/translation/trans_sentence_async

######请求参数(POST/GET)

| 参数名称 | 参数说明 |
|---|---|
| comcontext | 必须(只支持post)，String，答案 |
| solution | 必须(只支持post)，String，翻译的标准参考答案 |
| info_word | 选填(只支持post), JSON串，限定词, 格式如: '["word1", "word2"]' |
| access_token | 必须，这个token如何获取是通过[授权流程](../handbooks/workflows.html)得到这个token |
| scope | 必须，资源访问控制，固定值:all_json |
| ratio | 设置翻译对总分影响的比重,0-1之间 |
| config | 选填,自定义打分配置项(高级版功能) |

>config 格式如下:

```
{
    "word_cnt": { //字数限制
        "low": 20,
        "high": 100
    }
}
```

###资源scope
根据`appkey`的作用范围，开发者每次通过认证获取的access_token都有一个作用域`scope`,决定了该token能否从批改网的api获取正确的返回值。

###返回结果
######成功
```json
{
    "error": "success",
    "error_code": 0,
    "error_description": "success",
    "data": {
        "key": "PG2_emoapp_a345dd676b7975b22f45cc85c775bd90"
    }
}
```
######失败
```json
{
    "error": "原因",
    "error_code": 21329,
    "error_description": "原因描述"
}
```

######异步返回数据格式
> 注: 下面的分析数据将会以POST方式push到接入方指定的接收接口。
>
> header设置：Content-Type: application/json

```json
{
    "score": 77.5, // 总得分
    "score_cat": {
        "1": {
            "name": "词汇",
            "score": 0.8957009
        },
        "2": {
            "name": "句子",
            "score": 0.7107325
        },
        "3": {
            "name": "篇章结构",
            "score": 0.7091833
        },
        "4": {
            "name": "内容相关",
            "score": 0.6562703
        }
    },
    "comment": "文中词汇表达灵活多样请继续保持，高级词汇积累也做的很棒；从句数量偏少；全文结构较为严谨。", // 总评
    "sentences": [ // 逐句分析
        {
            "sid": 0, // 第几句
            "pid": 1, // 段落, 从1开始
            "text": "Urbanization presents a process that rural people migrate into the cities.",
            "comment": [
                {
                    "class": "error",
                    "cat": "句子错误",
                    "msg": "本句语法不规范，请检查。",
                },
                {
                    "class": "error_trp",
                    "cat": "拓展辨析",
                    "msg": "<b>people</b>表示“人，民族”。查看与<a href=\"http://bbs.pigai.org/forum.php?mod=viewthread&tid=12942&page=1&extra=#pid30263\" target=\"_blank\"><b>person</b></a>的区别。",
                }
            ]
        },
        {
            "sid": 1,
            "pid": 1,
            "text": "A key index of a nation’s urbanization level is the distribution of its population in urban a nd rural areas.",
            "comment": [
                {
                    "class": "error_trp",
                    "cat": "学习提示",
                    "msg": "<b>nation</b>表示“国家”。注意与<b>country</b>的区别。<a href=\"http://bbs.pigai.org/forum.php?mod=viewthread&tid=12961&page=1&extra=#pid30320\" target=\"_blank\">详情点击</a>",
                }
            ]
        },
        {
            "sid": 2,
            "pid": 1,
            "text": "The urbanization rate of China registered over 50% last year, which marks that our country has stepped into a new “city-based society”.",
            "comment": [
                {
                    "class": "error", // 分为四类: warn 警告，error 错误，error_trp 提示，great 好
                    "cat": "大小写错误",
                    "msg": "确认 <b>city-based</b> 大小写使用正确。",
                },
                {
                    "class": "error_trp",
                    "cat": "学习提示",
                    "msg": "易混词汇: <b>new</b>, fresh, novel, original, innovative 均含“新的”之意。",
                }
            ]
        },
        {
            "sid": 3,
            "pid": 1,
            "text": "Urbanization becomes an importa nt part of our society and economic development.",
            "comment": [
                {
                    "class": "error",
                    "cat": "词语错误",
                    "msg": "请检查<b>importa</b>，确认拼写正确。",
                },
                {
                    "class": "error_trp",
                    "cat": "学习提示",
                    "msg": "易混词汇: <b>development</b>, evolution 都表示“发展，进化”之意。",
                }
            ]
        },
        {
            "sid": 4,
            "pid": 1,
            "text": "By offering better education and job opportunities to urban citizens, it not only improves people’s living standards, but also it makes their culture colorful.",
            "comment": [
                {
                    "class": "error_trp",
                    "cat": "拓展辨析",
                    "msg": "动名搭配 <b>improve...standard</b> 在语料库中出现过 <a target=\"_blank\" href=\"corpus/snt/?q=improve standard/von\">193</a> 次",
                }
            ]
        }
    ],
    "meta_data": { //自定义参数
        "xxx": "xxxx"
    },
    "key": "PG2_emoapp_bb1f71d46d236eb3819a41c59eca920d"
}
```
