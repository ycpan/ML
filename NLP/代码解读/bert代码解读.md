<!-- TOC -->

- [bert代码解](#bert%e4%bb%a3%e7%a0%81%e8%a7%a3)
    - [对输入数据的处理](#%e5%af%b9%e8%be%93%e5%85%a5%e6%95%b0%e6%8d%ae%e7%9a%84%e5%a4%84%e7%90%86)
    - [bert程序](#bert%e7%a8%8b%e5%ba%8f)
    - [训练](#%e8%ae%ad%e7%bb%83)
    - [bert的优缺点](#bert%e7%9a%84%e4%bc%98%e7%bc%ba%e7%82%b9)

<!-- /TOC -->
#   bert代码解

bert代码主要集中在run_pretraing.py与modelling.py中
run_pretraing.py主要负责读取生成好的tf.data训练数据，调用Transformer encoder部分代码，生成预测与损失函数。
modelling.py主要包含Transfomer encoder代码，Multihead attention代码等。

bert输入数据：
![bert-input](bert-input.png)
input:最原始的数据输入，需要转化为数字向量，数字向量包括：
TokenEmbeddings:token 字符查表对应的向量   
Segment Embeddings:分句向量，用于区分上下句。一般用数字0和1表示。   
Position Embeddings:在bert程序中，位置编码是通过训练得到的，和Transformer中的不太一样。


###   对输入数据的处理
主要在：create_pretraining_data.py中的create_instances_from_document函数中。  
 如何生成上下句对
主要在create_traing_data.py中的create_instances_from_document.首先将输入文档以'.'号分割，形成list列表，取名为document;然后随机打乱；最后设置一个长度，target_sequence_length.如果积累的字符份个数（用current_chunk存储，用current_length表示积累的字符个数）大于target_sequence_length时或者遍历完整个document时候，开始切分句子。对于短句，可能好几个句子才能表示上一句话，好几个句子才能表示下一句话，因此，模型不会因为 '.'作为划分句子间隔的标准;另一方面，对于长句，可能中间就没有点号，就要切分上一句与下一句。   
如何生成mask
生成mask主要在cretae_trainging_data.py中的create_masked_lm_predictions中。    
###  bert程序
程序在modeling.py中的BertModel中。里面包含了 Transformer 与 Attentiton.

### 训练 
模型训练的程序在run_pretraining.py中model_fn中
### bert的优缺点
BERT中的位置编码是训练出来的，和transformer不同。
在multiattention后，bert有一个全链接层，然后resudual+laynerNormal，原论文中没有。
BERT的输入只有TokenEmbeddings(input_id),(Segment Embeddings)segment_id,Positions Embeddings(mask_id)
优缺点：
BERT算法还有很大的优化空间，例如我们在Transformer中讲的如何让模型有捕捉Token序列关系的能力，而不是简单依靠位置嵌入。
BERT的训练在目前的计算资源下很难完成
BERT在MLM任务中的mask策略对真实的单词产生偏见。目前还未显示这种偏见对训练的影响。