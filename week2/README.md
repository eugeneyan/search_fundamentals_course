## Lessons from this week's project

- Why have keyword field on text? 
	- Because we want to use the keyword field for aggregations: "Most users who want to do more with text fields use [multi-field mappings](https://www.elastic.co/guide/en/elasticsearch/reference/7.10/multi-fields.html "fields") by having both a `text` field for full text searches, and an unanalyzed [`keyword`](https://www.elastic.co/guide/en/elasticsearch/reference/7.10/keyword.html "Keyword type family") field for aggregations" (more [details](https://www.elastic.co/guide/en/elasticsearch/reference/7.10/text.html#before-enabling-fielddata))

- Difference between match on `name` vs. `name.hyphens`
    - ```
        "name": {
        "type": "text",
        "analyzer": "english",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 2048
          },
          "hyphens":{
            "type": "text",
            "analyzer": "smarter_hyphens"
          }
        }
      },
      ```
    - `name`: A regular text field. When we query "I-phone" on it, the results mostly contain items where the name field contain "-"
    - `name.hypen`:  Also a text field, but with the `smarter_hypens` analyzer which has a (i) tokenizer that only tokenizes on whitespace and newline, and (ii) a filter which concatenates all tokens separated by non-alphanumeric chars (and other good stuff from word delimiter graph token)
        - "i-phone" tokenizes to -> ["i-phone"] and filters to -> ["i-phone", "iphone"]
        - "i phone" tokenizes to -> ["i", "phone"] and filters to -> ["i", "phone"]
        - "iPhone4" tokenizes to -> ["iPhone4"] and filters to ["iphone4", "iphone", "i", "phone", "4"]
        - "Iphone4" tokenizers to ["Iphone4"] and filters to ["iphone4", "iphone", "4"]
