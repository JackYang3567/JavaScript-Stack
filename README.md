# MongoDB
## 9、 MongoDB 使用地理空间索引
- 文档必须有一个子对象或数组(第一个元素指定对象类型，紧接着是该元素的经度和纬度)，如：
```
    "loc": {
        "type": "Point"
        "coordinates": [
            "0": 104.066414
            "1": 30.618113
        ]
    }
```

- 创建地理空间信息的索引
```
db.orders.ensureIndex({loc:"2dsphere"})
或 db.orders.createIndex({loc:"2dsphere"})
```

- 删除地理空间信息的索引
```
db.orders.dropIndex("loc_2dsphere")
```

-查询地理空间信息
```
db.orders.find({"snapshot.loc.coordinates": [ 104.066414, 30.618113]})
```
