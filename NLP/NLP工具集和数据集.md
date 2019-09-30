#NLP工具集和数据集

​		介绍一些在NLP各种领域（area）和领域内任务（task）的一些第三方库以及目前在各个领域、任务上使用的数据集。

## 第三方库

介绍一些可以快速在项目中使用的NLP，实现NLP任务的第三方开发库。

#### jieba

类似的分词可以看清华大学的分词库，对各个分词有对比介绍：http://thulac.thunlp.org/

但是jieba简单易用。

项目地址：https://github.com/fxsjy/jieba；[cpp版本](https://github.com/yanyiwu/cppjieba)

##### 使用范围

- 分词
- 关键词提取
  - TF-IDF
  - [TextRank](http://web.eecs.umich.edu/~mihalcea/papers/mihalcea.emnlp04.pdf)
- 词性标注
- 并行分析
- Tokenize

#### FastText

项目地址：[https://fasttext.cc](https://fasttext.cc/)

#####使用范围：

- Word representation
- Text classification

#####附加功能：

- 提供很多已经训练好的[词向量](https://fasttext.cc/docs/en/crawl-vectors.html)，各种语言；

#####强项：

未知；

#####弱项

至少在自己的数据集上训练的中文词向量是弱于用gensim训练的结果。

#### gensim

一个python库，自动抽取文档语义信息。处理无结构化的文本数据。

项目地址：https://radimrehurek.com/gensim/intro.html

##### 使用范围

- Word representation（sentence representation、document representation）
- extract semantic topics

以及基于这些representation和topics的查询，相似度计算等。

#### spaCy

项目地址：https://spacy.io/

更全面的库，可以和gensim、tf，torch、sklearn等无缝结合。

**使用范围：**

- 分词
- NER
- pre-trained word vectors
- POS
- dependency parsing
- sentence segmentation

也实现了[fasttext的word representation](https://radimrehurek.com/gensim/models/fasttext.html#gensim.models.fasttext.FastText)算法，



