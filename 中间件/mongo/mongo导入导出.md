发布的时候需要变更数据，最好备份下
-----------------

①：备份后,新增的数据在恢复时不会被删除。

②：如果存在同样的数据，恢复的时候会被覆盖掉，不会重复插入。

③：如果备份后某条数据被update了，恢复的时候备份后倘若某条记录被更新了，无法恢复到备份点的状态

导出的是文件夹
`mongodump -h127.0.0.1:27017 -d msc-db -c t_tenant_i18n -u msc -p abc123 --oplog -o msc-db/`

--oplog只适合于副本集，备份时候记录期间的数据变化，恢复时会更新这些变化的数据
------------------------------------------

**用于集群**
```
mongodump -h127.0.0.1 --port 20000 --authenticationDatabase=admin --gzip -dfeedworkBid -cLEANWORK_XAUUSD_W1 -uadmin -pWAer8R59G6 -o /root/

mongorestore --gzip  导入压缩的备份
mongorestore -h10.47.43.22 -urakeback -pGqTEt12E -drcr_t000138 --authenticationDatabase=admin --gzip dump_t000138/rcr_t000138/finish_tickets.bson.gz
```
##  导入(恢复)
```
mongorestore -h127.0.0.1:27017 -d ops-db -c t_module_permission -u ops -p abc123  /root/ops-db/t_module_permission.bson
mongorestore -h10.47.43.22:27017 -ubrokerwork -po6RJzAF1LELzutEv -dbrokerwork /root/brokerwork20170416/brokerwork
备注：mongodump 如果不指定 -d 参数，刚会备份整个 MongoDB 实例。
mongorestore -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 --drop 文件存在路径
mongorestore -h127.0.0.1 --port 27017 -uadmin -pWAer8R59G6 --authenticationDatabase=admin -dtest --drop /root/test/

### --drop

#### 恢复的时候，先删除当前数据(包括索引)，然后恢复备份的数据。就是说，恢复后，备份后添加修改的数据都会被删除，慎用哦！所以使用drop的导入的时候应该bson和json应该一块导入
```
直接执行文件,文件名改为js结尾(文件里面是一些执行语句)
-----------------------------
mongo 127.0.0.1:27017/msc-db -umsc -pabc123 flag.js

## **导出的是文件**
mongoexport/mongoimport
将collection导出为json/csv格式数据/将数据导入数据库,mongoimport恢复会直接覆盖这张表
`mongoexport -h10.25.205.183:27017 -u owsc -pDS3x8mHP  -d owsc -c t_structural_setting -q '{"tenantId":"T001172", "productId":"TW"}' -o chaxun.txt`
`mongoimport -h10.28.185.68:27017 -dbrokerwork -ct_customer_profiles -ubrokerwork -po6RJzAF1LELzutEv tony.txt`

## **--csv 以EXCEL格式导出**

## **导出某个字段**
```
mongoexport -h10.25.205.183:27017 -uowsc -pDS3x8mHP -dowsc -ct_tenant -f _id,tenantName -q '{}' --csv -o tony.csv
mongodump -h10.28.185.68 --port 27017 -ubrokerwork -p'o6RJzAF1LELzutEv' -dbrokerwork -ct_account_base_info -q '{"tenantId" : "T001194"}'  -o T001194
```
带条件的导出

统计客户数
```
for db in `cat 2.txt`
do
	   echo $db >>1.csv
	   sed  "s/117/${db}/g" 1.js >2.js
	   mongo 10.47.43.22:27017/brokerwork -ubrokerwork -po6RJzAF1LELzutEv 2.js >> 1.csv
done
```
下面是1.js
```
db = db.getSiblingDB('brokerwork');
cursor = db.t_customer_profiles.count({"tenantId" : "117","enabled" : true,"createTime" : {"$gte":NumberLong(1483200000000)},"createTime" : {"$lte":NumberLong(1490976000000)}});
printjson(cursor);
```
直接打印出结果
```
db.getCollection('t_introduce_hit').find({"tenantId":"T001232"}).forEach(function(customer){
   print(customer.url);
});

db.t_tenant_product.find({"productId":{"$in":["BW","TW"]}}).forEach(function(products){
	db.t_tenant.find({"_id":products.tenantId}).forEach(function(tenants){
	 print(tenants.tenantName,products.tenantId,products.productId,products.productDomain,products.customerDomain);
		});
});
```