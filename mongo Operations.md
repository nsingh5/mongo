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
