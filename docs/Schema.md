# Apache Solr 入門 その2

* Chapter2 スキーマの定義についてメモ

## スキーマ定義

## スキーマって？

* RDBでいうところのテーブル(定義)です。: インデックス定義というらしい.
* 転置インデックスを作成する際に,投入データの形を知っておき正しく扱うのが目的
* スキーマを定義することでどのような形式のデータが入稿されどのように検索できるかを決めれます。
* つまり,検索の挙動を制御する非常にコアなものなのです.

## スキーマ定義ファイル

* スキーマファイル名は以下の通り(verによって異なります)
  * schema.xml: Solr6系未満
  * managed-schema: Solr6系以降
* コア内部にschema.xml, managed-schemaを混在させることはできず, 設定なしではschema.xmlの利用はできません。
* 利用するにはsolrconfig.xmlに記述す必要がある


### schema.xml/managed-schemaの違い

| 項目 | schema.xml | managed.schema |
| :--: | :----: | :-------: |
| 中身 | 同じ | 同じ |
| 編集の仕方 | エディタなどで直接書き換える | 直接書き換え or Schema APIの利用 |
| 反映 | コアの読み取りは起動時の１回なのでリロード行う | リロード不要 |


## スキーマの作成

### コアを作る

* Solrはコアの単位でインデックス定義・データ管理・プラグイン等を管理しています
  * つまり, 検索エンジンそのものをコアと呼んでも過言ではないですね。
  * さらに, スキーマを作成するためにはまずコアが必要です。
* コアの作成(UIからもできるP.23)

```
# Solrの起動
$ solr start

# コアの作成
$ solr create_core -c {コアの名前} -d {コアのベース}
``` 

* Solrにはデフォルトでコアの雛形があり最初はカスタマイズもなにもないのでデフォルトを指定すること。
  * Solr6系(未満)では`base_configs, data_driven_schema_configs, sample_techproducts_configs`の3つが存在
    * このうちの１つを指定してコアを作るが, 何も指定しないと`data_driven_schema_configs`が選択される。
  * Solr7系(以降?)ではこの3つが`_default`に集約されたので特に選ばなくても大丈夫です(選ぶなら _default と書くこと)
  * コアディレクトリが存在するPath: `solr/{ver}/server/solr/configsets/`
* 作成したコアは`solr/{ver}/server/solr/`配下に作られる

### スキーマ定義

* スキーマ定義ファイル(schema.xml/managed-schema)の配置場所は`solr/{ver}/{コアの名前}/conf/`とする(場所を考える必要はないが在り処は知っておくべき)
* 手順としては以下(2つ)

1. スキーマ定義ファイルに「フィールド」と「フィールドタイプ」を定義する
2. スキーマ定義を有効化する(schema.xmlのみ)

```
# スキーマ有効化(コアのリロード)
solr restart
```

### フィールドタイプ

* データの型(数値, 文字列, 日付)を表します
* データの保存をするために主に利用されます。

#### 定義方法

* Schema API

```
$ curl -X POST -H 'Content-type:application/json' --data-binary '{
  "追加/削除/更新の指定をするKey(下記表)" : {
    "name":"{フィールドタイプの名前}",
    "class":"フィールドタイプが使用するクラス名"
    "オプションKey":"Val",
    ....
  },
  "追加/削除/更新の指定をするKey(下記表)" : { 複数指定可能！ },
}' http://{solrサーバのドメイン}:{起動ポート番号}/solr/{コア名}/schema
```

* schema.xml(直接編集するとき. managed-schemaでも同じようにいけます)

```
<schema name="default-config" version="1.6">
  <fieldType name="{フィールドタイプ名}" class="{フィールドタイプが利用するクラス名}" {オプションKey}="{Val}" />
```

* 追加/削除/更新のKey名

| 種類 | Key名 |
| :--: | :---: |
| 追加 | add-field-type |
| 削除 | delete-field-type |
| 更新 | replace-field-type |

