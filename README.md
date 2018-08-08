# Gomycode elasticsearch workshop

This repo provides the instructions and examples from the demo highlighting the features of elasticsearch.

## Getting started
### Elastic Cloud
Launch an elasticsearch service instance on [elastic cloud](https://www.elastic.co/cloud/elasticsearch-service)

### Local
Using Docker, you can get up and running using the stack docker-compose file on [github](https://github.com/elastic/stack-docker) .

```bash
git clone https://github.com/elastic/stack-docker.git
docker-compose -f setup.yml up
docker-compose up -d elasticsearch kibana
```

# Analyzers
## English
```js
GET /_analyze
{
  "analyzer": "english",
  "text": "I will either find a way or make one."
}
```

## html strip + stop words
```js
GET /_analyze
{
  "char_filter": [
    "html_strip"
  ],
  "tokenizer": "standard",
  "filter": [
    "lowercase",
    "stop"
  ],
  "text": "I will either find a <em>way</em>  or make one."
}
```

## French
```js
GET /_analyze
{
  "analyzer": "french",
  "text": "Je trouverai un chemin, ou j’en créerai un."
}
```

# Synonyms
```js
PUT /hannibal
{
  "settings": {
    "analysis": {
      "filter": {
        "my_synonym_filter": {
          "type": "synonym",
          "synonyms": [
            "make,create"
          ]
        }
      },
      "analyzer": {
        "my_analyzer" : {
          "char_filter": [
            "html_strip"
          ],
          "tokenizer": "standard",
          "filter": [
            "lowercase",
            "stop",
            "my_synonym_filter"
          ]
        }
      }
    }
  },
  "mappings": {
    "_doc": {
      "properties": {
        "quote": {
          "type": "text",
          "analyzer": "my_analyzer"
        }
      }
    }
  }
}
```
```js
PUT /hannibal/_doc/1
{
  "quote": "I will either find a <em>way</em>  or make one."
}
PUT /hannibal/_doc/2
{
  "quote": "I have come not to make war on the Italians, but to aid the Italians against Rome."
}
PUT /hannibal/_doc/3
{
  "quote": "God has given to man no sharper spur to victory than contempt of death."
}
```

# Search
```js
POST /hannibal/_search
{
  "query": {
    "match_all": { }
  }
}
```
```js
POST /hannibal/_search
{
  "query": {
    "match": {
      "quote": "make"
    }
  }
}
POST /hannibal/_search
{
  "query": {
    "match": {
      "quote": "create"
    }
  }
}
```

# Score
```js
POST /hannibal/_search?explain=true
{
  "query": {
    "match": {
      "quote": "make"
    }
  }
}
```


