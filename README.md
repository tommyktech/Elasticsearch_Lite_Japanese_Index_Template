# Elasticsearch Lite Japanese Index Templates
Elasticsearchで日本語全文検索を軽量かつ簡単に扱うためのindex template。  
このテンプレートを使うと全文検索機能がデフォルトでオフになり、特定のフィールドだけにオンにすることが出来るようになります。  
また、全文検索フィールドで使われるanalyzerやmappingの設定を工夫することで、保存データ量が削減されるようになっています。  

## 使い方
1. `lite_template` の中身をコピーしてKibanaのDev Tools Consoleで登録
2. `lite_component` の中身をコピーして同じくDev Tools Consoleで登録
3. `lite_`を接頭辞(prefix)にした名前のindexを作成  
  例: `PUT /lite_sample_index`
4. 全文検索したいフィールドの名前に接尾辞(suffix)として`_txt`を付与してデータを格納する  
```
POST lite_sample_index/_doc/  
{  
  "title_txt": "本日は晴天なり"  
}  
```

5. 検索する
```
POST lite_sample_index/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "sample_txt": {
              "query": "本日は晴天なり",
              "boost": 1
            }
          }
        },
        {
          "match": {
            "sample_txt.kuromoji": {
              "query": "本日は晴天なり",
              "boost": 3
            }
          }
        }
      ]
    }
  }
}
```

## 仕組み
* テンプレート機能によってテキストデータはデフォルトでは`keyword`として格納され、`_txt`が末尾についたフィールドのみ全文検索が適用されるため、格納されるデータ量が抑えられる
* 末尾`_txt`のフィールドは、日本語の全文検索を軽量かつ十分に扱うための仕組みがいろいろと適用されるようになっている
  * analyzerはkuromojiとngramの両方が設定されているため、典型的な日本語だけでなく未知語でも対応できる
  * kuromojiの設定は最小限（lowercase, 助詞除外、半角全角揃え、かな揃え）
  * ngramは1-gramから3-gramまで
  * 全文検索フィールドのmappingには `"index_options" : "docs"` を適用することで、保存されるデータ量を削減
