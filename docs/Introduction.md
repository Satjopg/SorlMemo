# Apache Solr 入門 その１

* 基本的にApache入門の本の進み方に沿ってメモ。
* このdocは Chapter1 イントロダクション について。

## Apache Solrとは

* 全文検索エンジンであるApache Luceneを用いて作られた完成された検索エンジンサーバ(Jettyで動いているっぽい)
* 転置インデックス方式で検索を実行
  * 転置インデックス: ドキュメントを単語(形態素)にパースし, Map<単語,List<ドキュメントID>>として整理しなおしたもの
  * 重み付け: TF-IDF or BM25を用いてドキュメントに重みをつけることにより検索ワードに関連の深いドキュメントを上位に置くことができる

## 起動方法

* Macでやってます
* Java・brewが入っていることが前提です
* UIの説明は本読んでくれ

* Solrインストール

```
$ brew install solr
```

* Solrサーバ起動 -> アクセス: localhost:8983/solr/

```
$ solr start

# サンプルデータ投入しコア作成
$ solr start -e techproducts
```

* Solr停止

```
solr stop -p 8983
```
