---
title: LDA主题建模
date: 2018-01-26 15:23:34
tags: LDA
categories:
		-  机器学习和深度学习
---
>本文首发于我的博客：[gongyanli.com](http://gongyanli.com/%E7%9F%A5%E8%AF%86%E5%9B%BE%E8%B0%B1%E4%B8%8E%E8%AE%A4%E7%9F%A5%E6%99%BA%E8%83%BD-%E8%82%96%E4%BB%B0%E5%8D%8E/)  

前言:本文用到的方法叫做主题建模（topic model)或主题抽取(topic extraction)，在机器学习的分类中，它属于非监督学习(unsupervised machine learning)。它是文本挖掘中常用的主题模型，用来从大量文档中提取出最能表达各个主题的一些关键词。
主题模型定义(维基百科)：在机器学习和自然语言处理等领域是用来在一系列文档中发现抽象主题的一种统计模型。

- 主题抽取方法：目前最为流行的是隐含狄利克雷分布(Latent Dirichlet Allocation--LDA)
- LDA需要人为设定主题的数量，而我们怎么知道一堆文章里究竟有多少个主题呢？

	如果只需要把文章粗略划分为几个分类，那可以把数字设定小一些；
	如果希望能识别出非常细分化的主题，那就增大主题的个数；
	如果对于划分的结果不够满意，那可以继续迭代，调整主题数量来进行优化；

## 步骤
1.文本分词

	英文：split()都可以
	中文：推荐jieba分词
2.构建主题模型
	
	（1）构建词典
	（2）对应text的向量(word2vec1)
	 (3) 统计tdidf
	（4）对应的text的向量(word2vec2)
	（5）LDA模型
	（6）LDA特征向量
	 代码中得到的corpus_lda就是每个text的LDA向量，稀疏的，元素值隶属于对应叙述类的权重


## 代码
注意：最好在linux下运行，我在windows下运行的时候，出现以下错误：
	
	ImportError: [joblib] Attempting to do parallel computing without protecting your import on a system that does not suppo
	rt forking. 
	To use parallel-computing in a script, you must protect you main loop using "if __name__ == '__main__'". 
	Please see the joblib documentation on Parallel for more information

我在python3下即使加了if __name__=='main'还是错误的，所以我换到了linux下。

	`import jieba
	import pandas as pd
	
	df = pd.read_csv("datascience.csv", encoding='gb18030')  # 打开文件
	
	
	# print(df.head())  # 默认读取文件的头5行
	# print(df.shape)   # 查看数据的维度
	
	# 使用jieba分词对文件进行分词
	def chinese_word_cut(text):
	    return " ".join(jieba.cut(text))
	
	
	df["content_cutted"] = df.content.apply(chinese_word_cut)
	# print(df.content_cutted.head()) # 查看文件是否已被正确分词
	
	from sklearn.feature_extraction.text import TfidfVectorizer, CountVectorizer
	
	'''
	    文本向量化：将文章中的关键词转换为一个个特征，然后统计关键词个数
	'''
	n_features = 1000  # 只提取1000个最重要的关键词
	tf_vectorizer = CountVectorizer(strip_accents='unicode',
	                                max_features=n_features,
	                                stop_words='english',
	                                max_df=0.5,
	                                min_df=10)
	tf = tf_vectorizer.fit_transform(df.content_cutted)
	
	from sklearn.decomposition import LatentDirichletAllocation
	
	n_topics = 5  # 设定主题个数
	lda = LatentDirichletAllocation(n_topics=n_topics, max_iter=50, learning_method='online', learning_offset=50.,
	                                random_state=0)
	result_topic = lda.fit(tf)
	print(result_topic)
	
	
	# 显示每个主题下的前若干关键词
	def print_top_words(model, feature_names, n_top_words):
	    for topic_idx, topic in enumerate(model.components_):
	        print("Topic #%d:" % topic_idx)
	        print(" ".join([feature_names[i] for i in topic.argsort()[:-n_top_words - 1:-1]]))
	        print()
	
	
	n_top_words = 20
	tf_features_names = tf_vectorizer.get_feature_names()
	print_top_words(lda, tf_features_names, n_top_words)
	
	import pyLDAvis.sklearn
	
	# 主题可视化
	vis = pyLDAvis.sklearn.prepare(lda, tf, tf_vectorizer)
	pyLDAvis.show(vis)`

## 出图

- 左侧圆圈代表不同的主题，圆圈大小代表每个主题分别包含文章的数量
- 右侧列出了频率最高的关键词列表，当把鼠标停在主题圆圈上的时候，关键词列表用不同的颜色表明当前关键词占全部文本关键词的比例

# 中文停用词