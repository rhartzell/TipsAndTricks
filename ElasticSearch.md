# ============  ElasticSearch API ==============

## install
```
brew update
brew instiall elasticsearch
start elastic search engine from bash shell prompt type => elasticsearch
```

## Cluster Health
```
curl -X GET "localhost:9200/_cat/health?v"
```

## Cluster Nodes
```
curl -X GET "localhost:9200/_cat/nodes?v"
```

## List all indices
```
curl -X GET "localhost:9200/_cat/indices?v"
```

## Create an index
```
curl -X PUT "localhost:9200/customer?pretty"
curl -X GET "localhost:9200/_cat/indices?v"
```

## Index a document
```
curl -X PUT "localhost:9200/customer/_doc/1?pretty" -H 'Content-Type: application/json' -d' {"name": "John Doe"}'
```

## Query a document
```
curl -X GET "localhost:9200/customer/_doc/1?pretty"
```

## Delete an Index
```
curl -X DELETE "localhost:9200/customer?pretty"
curl -X GET "localhost:9200/_cat/indices?v"
```

## jq command for prettifying json output in bash => 
```
brew install jq
```

# ============  Todo List API in Swagger ==============

## List all Todos
```
curl -s http://localhost:10010/ | jq
```

## Get a specific todo
```

curl -s http://localhost:10010/todo/1 | jq
```

## Add a new todo
```
curl -d '{"todo_id": 4, "todo": "Study Polish vocabulary.", "datecreated": "2018-12-08T22:31:25.380Z", "author": "Rod", "duedate": "2018-12-08T22:31:25.380Z"}' -H "Content-Type: application/json" -X POST http://localhost:10010/
```

## Delete a todo
```
curl -XDELETE http://localhost:10010/todo/4 | jq
```

## Update a todo
```
curl -d '{"todo_id": 4, "todo": "Study Polish vocabulary. Tomorrow!!!", "datecreated": "2018-12-08T22:31:25.380Z", "author": "Rod", "duedate": "2018-12-08T22:31:25.380Z"}' -H "Content-Type: application/json" -X PUT http://localhost:10010/todo/4
```
