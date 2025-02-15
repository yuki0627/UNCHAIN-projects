### 🌐 IC とは

コーディングに入る前に、IC と関連するワードについて簡単にお話ししましょう。

IC（Internet Computer） とは、トークンや dApps をホストするためのプラットフォームを提供する汎用ブロックチェーンです。2021 年に DFINITY 財団によりローンチ・オープンソース化されました。

DFINITY 財団は、IC を 2009 年の暗号通貨（ビットコイン）、2015 年のスマートコントラクト（イーサリアム）に続く 第 3 の革新であると述べています。

概念的には既存のインターネットを拡張したもので、アプリケーションを実行するためのリソースをコンピュータのグローバル・ネットワークで提供しようとするものです。これには、世界中の独立運営されるデータセンターが利用されています。

IC の目的は、従来の IT 技術（例えばビッグテックのクラウドサービスやファイルシステム、web サーバーなど）に代わりあらゆるシステムとサービスをホストし、完全にオンチェーン化された web3 を実現することです。

データの保存に AWS を利用するサービスや、ホストに従来の非分散的なサービスを用いていたアプリケーションも IC を利用することで、完全に分散化されたアプリケーションを構築することが可能になります。

### 📦 キャニスター

キャニスターとは、IC にホストされるスマートコントラクトのことです。コンパイルされた実行プログラムのほかに、データを保存するためのメモリを持つなど、従来のスマートコントラクトを進化させたものになっています。

### 🏭 サイクル

サイクルとは、イーサリアムにおける**ガス**のようなものでキャニスターが稼働するためのコストとして使われます。根本的な違いはイーサリアムは「ユーザーペイ」、IC は「スマートコントラクトペイ」（リバースガス）モデルを活用している点です。キャニスターにあらかじめ開発者がサイクルをチャージするため、エンドユーザーは自分が開始した計算の対価としてトークンを支払うことなく、サービスを利用することができます。

IC は ICP というユーティリティ・トークンを使用しています。サイクルは、このトークンから変換されたものです。

なぜ、ICP トークンではなくサイクルに変換する必要があるのでしょうか？

ICP トークンは市場価格により変動しますが、サイクルの価格はキャニスターの運用コストが予測可能であることを保証するために固定されています（コストについては[こちら](https://internetcomputer.org/docs/current/developer-docs/deploy/computation-and-storage-costs)）。開発者が IC 上で開発を行いやすくするためにサイクルを用います。

### 🙋‍♂️ 質問する

ここまでの作業で何かわからないことがある場合は、Discord の `#icp-static-site` で質問をしてください。

ヘルプをするときのフローが円滑になるので、エラーレポートには下記の 4 点を記載してください ✨

```
1. 質問が関連しているセクション番号とレッスン番号
2. 何をしようとしていたか
3. エラー文をコピー&ペースト
4. エラー画面のスクリーンショット
```

---

次のセクションに進み、開発の準備を始めましょう！
