# Elasticsearch Index Templates
普段使ってるElasticsearchのindex templateを載せておくリポジトリ。

## kvs テンプレート
機能一覧：
* 日本語対応
* よみがな対応
* 全文検索デフォルトOFF対応

ESではデフォルトで全文検索がONになっているので、それをあえてデフォルトOFFにしてKey Valueストアのように使うためのテンプレート。メモリやディスクの使用量を削減するために役立つ。

Field名の末尾を_textなどにすれば全文検索できるようになるので、必要に応じて設定する。
