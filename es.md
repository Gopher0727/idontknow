# API 端点

## 写数据

> PUT  /[index]/_doc/1       → 写入/覆盖文档  
> PUT  /[index]/_create/1    → 创建文档（重复报错）  
> POST /[index]/_doc/        → 写入文档（自动生成 ID）  
> POST /[index]/_bulk        → 批量写入  

## 查数据

> GET  /[index]/_doc/1       → 按 ID 查  
> POST /[index]/_search      → 搜索  
> POST /[index]/_count       → 统计总数  
> POST /[index]/_explain/1   → 解释评分为何是这个值  

## 管理

> GET  /[index]/_mapping     → 查看索引结构  
> GET  /[index]/_settings    → 查看索引配置  
> POST /[index]/_analyze     → 测试分词效果  
> DELETE /[index]            → 删除索引  

## 集群/节点

> GET  /_cat/indices?v          → 查看所有索引  
> GET  /_cat/shards?v           → 查看分片  
> GET  /_cluster/health         → 集群健康状态  
> GET  /_nodes/stats            → 节点统计  

# 查询 DSL（Query Clause）

> match_all     → 返回全部文档 {"query": {"match_all": {}}}  
> match         → 全文搜索，IK分词后匹配 {"query": {"match": {"content": "胡同美食"}}}  
> term          → 精确匹配（keyword字段）{"query": {"term": {"author.name": "张三"}}}  
> range         → 范围查询（数字/日期）{"query": {"range": {"rating": {"gte": 4.5}}}}  
> ids           → 按ID查多个 {"query": {"ids": {"values": ["1","3"]}}}  
> wildcard      → 通配符搜索 {"query": {"wildcard": {"title": "Elastic*"}}}  
> bool          → 布尔组合查询（must/filter/must_not/should）  
> nested        → 嵌套数组查询（如comments字段）  
> exists        → 字段不为空 {"query": {"exists": {"field": "comments"}}}  

## bool 子句说明

> must          → 必须匹配（参与算分）  
> filter        → 必须匹配（不参与算分，更快，可缓存）  
> must_not      → 排除匹配  
> should        → 加分项（非必须）  

# 响应字段含义

> _index     → 索引名称  
> _id        → 文档唯一ID  
> _version   → 文档版本号（每次修改+1）  
> _source    → 你存入的原始JSON数据  
> _score     → 匹配度评分（越高越相关）  
> result     → "created"新建 / "updated"更新  

**_version 乐观锁**：

> 读→改→写时指定版本号防止并发覆盖  
> PUT /[index]/_doc/1?if_seq_no=0&if_primary_term=1  

# 数据格式

> .ndjson / .jsonl     → 每行一个独立JSON，ES Bulk API用（如demo.es）  
> .json                → 一个文件只能有1个JSON对象  
> Bulk格式必须两行一组：{"index": {"_id": "1"}} \n {实际数据}  

# Kibana Dev Tools vs REST Client (.http)

> Kibana: POST /index/_search（省略主机，自动补全Content-Type）  
> REST Client: POST http://127.0.0.1:9200/index/_search（完整URL，手动写Header）  
> 查询DSL部分完全一致  

# IK分词

> 安装: elasticsearch-plugin install https://release.infinilabs.com/analysis-ik/stable/elasticsearch-analysis-ik-7.17.4.zip  
> mapping中指定: "title": {"type": "text", "analyzer": "ik_max_word"}  
> 测试分词: POST /[index]/_analyze {"analyzer": "ik_max_word", "text": "内容"}  
