---
layout: post
title: "elasticsearch 之mapping"
date: 2020-05-12 14:32:03
categories: Elasticsearch
excerpt: Elasticsearch
---

* content
{:toc}

### 下载安装Elasticsearch 和kibana

相对于数据库，Elasticsearch的强大之处就是可以模糊查询。具体安装过程就不细细描述了

	docker run -it --name elasticsearch -d -p 9200:9200 -p 9300:9300 -p 5601:5601 elasticsearch

	docker run -it -d -e ELASTICSEARCH_URL=http://127.0.0.1:9200 --name kibana --network=container:elasticsearch kibana



### mapping说明

docker安装的版本ES、kibana版本均是7.5.1. ES的mapping非常类似于静态语言中的数据类型：声明一个变量为int类型的变量， 以后这个变量都只能存储int类型的数据。同样的， 一个number类型的mapping字段只能存储number类型的数据。

同语言的数据类型相比，mapping还有一些其他的含义，mapping不仅告诉ES一个field中是什么类型的值， 它还告诉ES如何索引数据以及数据是否能被搜索到。

当你的查询没有返回相应的数据， 你的mapping很有可能有问题。当你拿不准的时候， 直接检查你的mapping。

如何查看当前数据库里面的mapping（你可以用postman或者浏览器访问以下链接）

     kibana请求	GET /product1/_mapping

	返回结果：

		{
		  "product1" : {
		    "mappings" : {
		      "properties" : {
			"brand" : {
			  "type" : "long"
			},
			"categories" : {
			  "type" : "long"
			},
			"comprehensive" : {
			  "type" : "long"
			}
		      }
		    }
		  }
		}


#### mapping的创建及修改方式

1.第一种创建方式	

就是直接插入数据，然后ES根据插入数据格式自动识别创建mapping，这种傻瓜式的方式非常简便，适合初学者。

如，创建一个ID为1 的新对象： http://127.0.0.1:9200/product/_doc/1

	kibana请求 PUT /product1/_doc/1
	{
	"brand": 333,
	"categories": 222,
	"comprehensive": 11

	}

2. 第二种创建方式

	   kibana请求 PUT product1

		{ 
		  "mappings": {
		      "properties": {
				"brand": {
					"type": "integer"
				},
				"categories": {
					"type": "long"
				},
				"comprehensive": {
					"type": "long"
				}
			}
		    }

		}


3. 第三种创建方式

已经创建了index（库已经创建），新增一个mapping

URL:http://127.0.0.1:9200/product1/_mapping？pretty PUT方式

注意，这种创建方式type[表名]是在URL中指定的，BODY部分只是指定了表结构：

		kibana请求	PUT /product1/_mapping?pretty
		{

		"properties": {
		"brand": {
			"type": "long"
		},
		"categories": {
			"type": "long"
		},
		"comprehensive": {
			"type": "long"
		}
	}

	}

#### mapping的修改

由于Elasticsearch底层使用了lucene的原因，不支持对mapping的修改，可使用索引重建的方式，步骤如下：

1，使用正确的mapping新建索引和类型

     如需要将旧索引的itemId字段改为keyword类型，则执行以下请求：。

    URL:http://127.0.0.1:9200/product2  PUT方式

    设置mapping:

    URL:http://127.0.0.1:9200/product2/_doc/_mappings?pretty  POST方式

	POST product2/_doc/mappings?pretty
	{
	  "mapping": {
	    "properties": {
	      "brands": {
		"type": "long"
	      },
	      "categories": {
		"type": "long"
	      },
	      "comprehensive": {
		"type": "text"
	      }
	    }
	  }
	}

    
2. 2，使用reindex api将旧索引数据导入新索引

索引重建后可使用reindex命令迁移数据，如将product1数据迁移至product2请求如下：

		POST /_reindex
		{
		  "source": {
		    "index": "product1",
		    "query": {
		      "match_all": {}
		    }
		  }, 
		  "dest": {
		    "index": "product2"
		  }
		}
3，删除老索引

	DELETE product1
	
将旧索引别名迁移到新索引请求：

  

		POST /_aliases

		{
		    "actions" : [
		        { "remove" : { "index" : "product", "alias" : "item_alias" } },
		        { "add" : { "index" : "product2", "alias" : "new_product" } }

		    ]

		}

4. 删除旧索引

		DELETE /product1
	
5. 创建索引，索引名为原来的索引名

		PUT product1
		{
		  "mappings": {
		    "properties": {
		      "brand": {
			"type": "long"
		      },
		      "brands": {
			"type": "long"
		      },
		      "categories": {
			"type": "long"
		      },
		      "comprehensive": {
			"type": "long"
		      }
		    }
		  }
		}

6. reindex将数据复制回去

		POST /_reindex
		{
		  "source": {
		    "index": "product2",
		    "query": {
		      "match_all": {}
		    }
		  }, 
		  "dest": {
		    "index": "product1"
		  }
		}

7. 删除那个创建的临时索引

		DELETE product2
