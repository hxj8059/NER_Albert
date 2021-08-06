# albert-chinese-ner

## Intro

基于albert中文预训练模型fine-tune的NER，可以方便采用自己的数据集训练，已训练模型包括以下labels
```bash
["O","B-NAME","E-NAME","B-CONT","I-CONT","E-CONT",
"B-RACE","E-RACE","B-TITLE","I-TITLE","E-TITLE",
"B-EDU","I-EDU","E-EDU","B-ORG","I-ORG","E-ORG",
"I-NAME","B-PRO","I-PRO","E-PRO","S-RACE","S-NAME",
"B-LOC","I-LOC","E-LOC","I-RACE","S-ORG","[CLS]","[SEP]"]
```

## Resources

- [Bert](https://github.com/google-research/bert)
- [ALBert](https://github.com/google-research/albert)
- [ALBert_zh](https://github.com/brightmart/albert_zh)

## Environment
tensorflow > 1.13，不支持tf2.0， 使用的tf1.15  
4 核 CPU  
8.19 GB 显存 2080Ti × 1  

## How to run the code

### Train

#### Preparation
下载[ALBert_zh](https://github.com/brightmart/albert_zh)中的base model，将模型文件夹重命名为albert_base_zh，放入项目中

1. 训练数据放在data文件夹内，分别为train.txt, dev.txt, test.txt(采用BIEOS标注法)
2. 删除albert_base_ner_checkpoints文件夹
3. 替换albert_ner.py中的31为len(labels)+1
4. 运行
   ```bash
   python albert_ner.py --task_name ner --do_train true --do_eval true --data_dir data --vocab_file ./albert_config/vocab.txt --bert_config_file ./albert_base_zh/albert_config_base.json --max_seq_length 64 --train_batch_size 32 --learning_rate 2e-5 --num_train_epochs 3 --output_dir albert_base_ner_checkpoints
   ```
5. 模型保存在albert_base_ner_checkpoints中

### Predict
#### Preparation
下载已fine-tune的[模型](https://openbayes.com/storages/content?key=hxj8059%2Fjobs%2F1022mh3lz1kb%2Foutput%2Fner%2Falbert-chinese-ner%2Falbert_base_ner_checkpoints%2F&name=albert_base_ner_checkpoints.output.zip&token=eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJoeGo4MDU5L2pvYnMvMTAyMm1oM2x6MWtiL291dHB1dC9uZXIvYWxiZXJ0LWNoaW5lc2UtbmVyL2FsYmVydF9iYXNlX25lcl9jaGVja3BvaW50cy8iLCJleHAiOjE2MjgyNTgzNTF9.WYqC_MA6u32H7BAbBckFqqiTFNQ_OfdRMk9OK0Xg7dqXroZVwcjo_Tld20v5R-va2HwZr6LYaWa0WysyO_EmEg)(链接可能失效)，并确保文件夹名为albert_base_ner_checkpoints
1. 将data文件夹中的test.txt换成要预测的数据(采用BIEOS标注法，默认标记为O)
2. 运行
   ```bash
   python albert_ner.py --task_name ner --do_predict true --data_dir data --vocab_file ./albert_config/vocab.txt --bert_config_file ./albert_base_zh/albert_config_base.json --max_seq_length 64 --learning_rate 2e-5 --output_dir albert_base_ner_checkpoints
   ```
3. 结果保存在albert_base_ner_checkpoints的label_test.txt中

## Result

训练3个epoch后：

```bash
eval_precision = 0.953054
eval_recall = 0.9631808
```

测试结果：

[CLS]
包
兵
华
先
生
:
国
籍
中
国
，
管
理
学
硕
士
。
历
任
长
城
证
券
股
份
责
任
公
司
量
化
投
资
部
研
究
员
...

[CLS]
B-NAME
I-NAME
E-NAME
O
O
O
B-CONT
E-CONT
I-CONT
I-CONT
O
B-PRO
I-PRO
E-PRO
B-EDU
E-EDU
O
O
O
B-ORG
I-ORG
I-ORG
I-ORG
I-ORG
I-ORG
I-ORG
I-ORG
I-ORG
E-ORG
B-TITLE
I-TITLE
I-TITLE
I-TITLE
I-TITLE
I-TITLE
I-TITLE
E-TITLE

## Warning

1. test.txt文本不能有英文的逗号，预测时会自动忽略英文逗号，导致最终字符串不匹配
2. 如果OOM，可以调整batch_size和max_seq_length，如果报错，albert_ner中的参数也要更改
