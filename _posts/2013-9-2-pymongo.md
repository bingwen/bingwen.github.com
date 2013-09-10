---
layout: default
---
###python中使用pymongo来连接mongo数据库  

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
	db.find({'name':'zhangsan'})
	
	#统计
	db.find().count()
	
	#排序
	db.find().sort("name")
	db.find().sort("name", pymongo.DESCENDING)
	
	#增
	db.insert({'name':'lisi'})
	
	#删
	db.remove({'name':'zhangsan'})
	
	#改
	db.update({'name':'zhangsan'},{"$set":{"email":"zhangsan@xx.com"}})
	
	
###mongo 创建collextion
db.createCollection("mytestdb ", {capped:true, size:10000}) 单位是kb