---
html: basic-data-types.html
parent: protocol-reference.html
blurb: アドレス、レジャーインデックス、通貨コードなどの基本的なデータタイプのフォーマットと意味について説明します。
---
# 基本的なデータ型

さまざまなタイプのオブジェクトがそれぞれ異なる方法で一意に識別されます。

[アカウント](accounts.html)は`"r9cZA1mLK5R5Am25ArfXFmqgNwjZgnfk59"`のような[アドレス][]で一意に識別されます。アドレスは常に「r」で始まります。`rippled`メソッドの多くは、16進数表記に対応しています。

[トランザクション](transaction-formats.html)は、トランザクションのバイナリフォーマットの[ハッシュ][]で識別されます。また、トランザクションは送信アカウントと[シーケンス番号][]でも識別できます。

閉鎖された各[レジャー](ledger-data-formats.html)は、[レジャーインデックス][]と[ハッシュ][]値を保有します。[レジャーを指定する](#レジャーの指定)場合、いずれか1つを使用できます。

## アドレス
[アドレス]: #アドレス

{% include '_snippets/data_types/address.ja.md' %}
<!--{#_ #}-->


## ハッシュ
[ハッシュ]: #ハッシュ

{% include '_snippets/data_types/hash.ja.md' %}
<!--{#_ #}-->

### ハッシュプレフィクス
[[ソース]](https://github.com/XRPLF/rippled/blob/master/src/ripple/protocol/HashPrefix.h "Source")

多くの場合、XRP Ledgerではオブジェクトのバイナリデータに4バイトのプレフィクスを付けてからハッシュを計算するため、異なるタイプのオブジェクトが同じバイナリフォーマットである場合でも、異なるハッシュが設定されます。既存の4バイトコードは、ASCIIでエンコードされた英字3文字の後に0バイトが続く構成となっています。

ある種のハッシュは、APIのリクエストとレスポンスに使用されます。またある種のデータに署名するときの最初のステップで計算されるだけのものや、より高度なハッシュを計算するためのものもあります。XRP Ledgerで使用されるすべての4バイトのハッシュプレフィクスは以下の表の通りです。

| オブジェクトタイプ                             | APIフィールド                            | ハッシュプレフィクス（16進数） | ハッシュプレフィクス（テキスト） |
|:--------------------------------------------|:---------------------------------------|:--------------------------|:--|
| コンセンサスの提案                             | なし                                    | `0x50525000`              | `PRP\0` |
| レジャーバージョン                             | `ledger_hash`                          | `0x4C575200`              | `LWR\0` |
| レジャー状態データ                             | `account_state` （[レジャーヘッダー][]内） | `0x4D4C4E00`              | `MLN\0` |
| レジャーデータ内部ノード                        | なし                                    | `0x4D494E00`              | `MIN\0` |
| レジャーデータ内部ノード（[SHAMapv2][]）         | なし                                    | `0x494E5200`              | `INR\0` |
| Payment Channelのクレーム                     | なし                                    | `0x434C4D00`              | `CLM\0` |
| 署名済みのトランザクション                       | トランザクションの`hash`                  | `0x54584E00`              | `TXN\0` |
| メタデータを持つトランザクション                  | なし                                    | `0x534E4400`              | `SND\0` |
| 未署名のトランザクション（シングル署名）           | なし                                    | `0x53545800`              | `STX\0` |
| 未署名のトランザクション（マルチシグ）             | なし                                    | `0x534D5400`              | `SMT\0` |
| 検証の投票                                    | なし                                    | `0x56414C00`              | `VAL\0` |
| バリデータサブキー認証（「バリデータマニフェスト」） | なし                                    | `0x4D414E00`              | `MAN\0` |

[レジャーヘッダー]: ledger-header.html
[SHAMapv2]: known-amendments.html#shamapv2

[レジャーオブジェクトID](ledger-object-ids.html)も似た方法で計算されますが、ここで説明したプレフィクスの代わりに「スペースキー」という2バイトのプレフィクスを使用します。


## アカウントシーケンス
[シーケンス番号]: #アカウントシーケンス

{% include '_snippets/data_types/account_sequence.ja.md' %}
<!--{#_ #}-->


## レジャーインデックス
[レジャーインデックス]: #レジャーインデックス

{% include '_snippets/data_types/ledger_index.ja.md' %}
<!--{#_ #}-->


### レジャーの指定

APIメソッドの多くは、レジャーのインスタンスを指定する必要があります。その場合、共有されたレジャーの特定バージョンで最新と見なされるデータで指定する必要があります。レジャーバージョンを受け入れるコマンドは、すべて同様に機能します。使用するレジャーを指定するには、以下の3つの方法があります。

1. `ledger_index`パラメータにレジャーの[レジャーインデックス][]を指定します。閉鎖された各レジャーには識別用のレジャーインデックスが付いていて、その前に検証されたレジャーより1つ大きい番号になります。（最初のレジャーのインデックスは1です。）

        "ledger_index": 61546724

2. `ledger_hash`パラメータにレジャーの[ハッシュ][]値を指定します。

        "ledger_hash": "8BB204CE37CFA7A021A16B5F6143400831C4D1779E6FE538D9AC561ABBF4A929"

3. `ledger_index`パラメータに以下のいずれかのショートカットを指定します。

    * `validated`: [コンセンサスで検証](consensus-structure.html#検証)された最新のレジャー

            "ledger_index": "validated"

    * `closed`: 変更できないように閉鎖され、検証を提案されている最新のレジャー

    * `current`: サーバーで現在処理中のレジャーバージョン

上記3つのフォーマットすべてを受け入れる、廃止予定の`ledger`パラメーターもあります。このパラメーターは使用*しないでください*。今後予告なしに廃止される可能性があります。

レジャーを指定しない場合、デフォルトで`current`（処理中）レジャーが選択されます。レジャーを指定しなかった場合、サーバはリクエストにどのレジャーを使うかを決めます。デフォルトでは、サーバは`current`(進行中)のレジャーを選択します。[レポートモード](rippled-server-modes.html#レポートモード)では、サーバは最新の検証済みレジャーを使います。レジャーを指定するフィールドは複数指定しないでください。

**注記:** レジャーを指定する際に上記のデフォルトの動作に頼らないでください。変更される場合があります。可能であれば、常にリクエストでレジャーバージョンを指定してください。

レポートモードでは、検証済みとなるまでレジャーデータは記録されません。レポートモードサーバに`current`または`closed`のレジャーをリクエストすると、サーバはそのリクエストを P2Pモードサーバに転送します。検証されていないレジャー番号またはハッシュをリクエストした場合、レポートモードのサーバは`lgrNotFound`エラーを返します。


### 通貨額の指定

XRP LedgerにはXRPとトークンの2種類の通貨があります。これら2種類の通貨は、異なるフォーマット、異なる精度と丸め動作で指定されます。

[Paymentトランザクション][]で送金する`Amount`のようないくつかのフィールドは、どちらのタイプにもすることができます。`Fee`フィールド（[トランザクションコスト](transaction-cost.html)）のように、XRPのみを使用可能なフィールドもあります。

XRPは、XRPの “drop"数を含む整数の文字列として指定され、100万ドロップが1XRPに相当します。トークンは、10進数の金額、通貨コード、発行者のフィールドを持つオブジェクトとして指定されます。

- **XRP** - `Amount`フィールドに13.1 XRPを指定するには:

        "Amount": "13100000"

- **トークン** - `rf1B...`が発行した13.1 FOOという値で`Amount`フィールドを指定するには：

        "Amount": {
            "value": "13.1",
            "currency": "FOO",
            "issuer": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn"
        }

詳しくは[通貨フォーマット](currency-formats.html)をご覧ください。


## 時間の指定

`rippled`サーバとそのAPIでは、時間を符号なし整数で表します。この数値は、「Rippleエポック」である2000年1月1日（00:00 UTC）から経過した秒数を表しています。これは[UNIXエポック](http://en.wikipedia.org/wiki/Unix_time)と同様に機能しますが、RippleエポックはUNIXエポックより946684800秒遅れています。

Rippleエポック時間を32ビット変数でUNIXエポック時間に変換しないでください。整数のオーバーフローが発生する恐れがあります。

<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}