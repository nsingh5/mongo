# mongo Operations

### GridFS:

GridFS is a feature of storing and retrieving files. For files larger than 16 MB this feature is very useful. GridFS divides a document in parts called chunks and stores them in a separate document. These chunks have a default size of 255kB except the last chunk.

bin/mongofiles -d troublecode put /Users/narendrasingh/Downloads/pkpadmi.pdf


### Data Backup and Restoration in MongoDB:

1)logical - mongo dump
2) physical -snapshots


mongodump/mongorestore - impact db r/w operations/traffic

shutdown and copy - shutdown one of shard and copy

filesystem snapshot- do not impact  r/w operations (hot backup)



![image](https://user-images.githubusercontent.com/10362252/210093497-17953aa3-057b-4483-b224-4429b31dabc7.png)


### Replication/replica set
docker network create mongoCluster

docker run -d --rm -p 27017:27017 --name mongo1 --network mongoCluster mongo mongod --replSet myReplicaSet --bind_ip localhost,mongo1

docker run -d --rm -p 27018:27017 --name mongo2 --network mongoCluster mongo mongod --replSet myReplicaSet --bind_ip localhost,mongo2

docker run -d --rm -p 27019:27017 --name mongo3 --network mongoCluster mongo mongod --replSet myReplicaSet --bind_ip localhost,mongo3

https://www.mongodb.com/compatibility/deploying-a-mongodb-cluster-with-docker


### sharding

https://www.youtube.com/watch?v=Rwg26U0Zs1o&list=PL34sAs7_26wPvZJqUJhjyNtm7UedWR8Ps&index=9

https://github.com/justmeandopensource/learn-mongodb/blob/master/sharding/00-setup-sharding-doc.md


shard key:

(write)

  high cardinality
  
  low freq
  
  no monotonically increase
  
(read)

  part of each doc.
  
  part of each query
  
  read isolation
  
https://university.mongodb.com/videos/y/-J7wzsdl9GE

range/hash
simple/compound

https://www.google.com/search?q=shard+key+mongodb&rlz=1C5GCEA_enIN965IN966&sxsrf=ALiCzsYm1h996bl-lywEunTfAX9vpBCqQg:1672575924452&source=lnms&tbm=vid&sa=X&ved=2ahUKEwjKz9jurqb8AhWb3jgGHbhlANsQ_AUoA3oECAIQBQ&biw=1280&bih=586&dpr=2#fpstate=ive&vld=cid:0053525e,vid:NjvyFmuuRM4




### Storage Engine
https://www.mongodb.com/docs/manual/core/storage-engines/
