---
layout: post
title: "python中使用pymongo来连接mongo数据库"
date: 2014-05-05 10:29:51
category: "技术"
tags: python mongo
author: bingwen
---

<!--break-->

	import pymongo  

	#创建连接
	
	connection = ReplicaSetConnection(SERVERS, replicaSet=REPLICA_SET_NAME, safe=True, w=2)
	db = connection[DB]
	db.authenticate(MONGO_USER,MONGO_PWD)
	coll = db[COLLECTION]
	
	#查询
	coll.find_one()
	coll.find()
	
	#条件查询
	coll.find({'name':'zhangsan'})
	
	#统计
	coll.find().count()
	
	#排序
	coll.find().sort("name")
	coll.find().sort("name", pymongo.DESCENDING)
	
	#增
	coll.insert({'name':'lisi'})
	
	#删
	coll.remove({'name':'zhangsan'})
	
	#改
	coll.update({'name':'zhangsan'},{"$set":{"email":"zhangsan@xx.com"}})
	
	
###mongo 创建collextion
	db.createCollection("mytestdb ", {capped:false, size:10000}) 单位是kb
	
	#capped 这货要注意了，如果设置成True，就固定了，也就是说你插入一个字典，这个字典的key就定死了，只能更改值，但不能插入新的key，也不能删除这个key

###条件查询
	coll.find({'user_id': {"$in": ids}})
