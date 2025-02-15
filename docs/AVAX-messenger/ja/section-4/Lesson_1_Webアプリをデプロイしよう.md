### 🎨 UI を完成させる

今回のプロジェクトでは,あなたは以下を達成しました 🎉

**1 \. `Messenger` コントラクトを作成する**

- Solidity でコントラクトを書く
- コントラクトをローカルでテストする

**2 \. ユーザーのウォレットを Web アプリケーションから接続して,コントラクトと通信する Web3 アプリケーションを作成する**

- MetaMask を設定する
- コントラクトを実際の Avalanche Fuji Network にデプロイする
- ウォレットを Web アプリケーションに接続する
- Web アプリケーションからデプロイされたコントラクトを呼び出す
- Web アプリケーションを介して AVAX を取引する

プロジェクトをレベルアップさせましょう！  
これまでのレッスンで作成した dapp を元にあなたのお好きなように機能を追加してみてください 💪 🚀

- CSS や文章を変更したり,画像や動画を自分の Web アプリケーションに乗せてみる
- コントラクトやフロントエンドに機能を追加してみる

### 🌍 サーバにホストしてみよう

最後に,[Vercel](https://vercel.com/) に Web アプリケーションをホストする方法を学びます。

Vercel はサーバレス機能のホスティングを提供するクラウドプラットフォームです。

スケーリングやサーバの監視は Vercel が行うため,開発者は Vercel へデプロイするだけでアプリケーションを公開・運用できます。

Vercel に関する詳しい説明は,[こちら](https://zenn.dev/lollipop_onl/articles/eoz-vercel-pricing-2020)をご覧ください。

まず,GitHub の `messenger-client` にあるローカルファイルを github へアップロードしましょう。

まだアップロードをしていない方は, ターミナル上で `messenger-client` に移動して,下記を実行しましょう。  
⚠️ `.gitignore`ファイル内に`.env`が記載されていることを確認していください。

```
git add .
git commit -m "upload to github"
git push
```

次に,GitHub 上の `messenger-client` とローカル環境に存在する`messenger-client` のファイルとディレクトリが反映されていることを確認してください。

Vercel のアカウントを取得したら,下記を実行しましょう。

1\. `Dashboard` へ進んで,`New Project` を選択してください。

![](/public/images/AVAX-messenger/section-4/4_1_1.png)

2\. `Import Git Repository` で自分の GitHub アカウントを接続したら,`messenger-client(あなたのレポジトリの名前)` を選択し,`Import` してください。

![](/public/images/AVAX-messenger/section-4/4_1_2.png)

3\. プロジェクトを作成します。

デフォルトの設定で良いはずですが,  
もし`FRAME WORK PRESET`が`Next.js`になっていない場合は`Next.js`を選択してください。

![](/public/images/AVAX-messenger/section-4/4_1_3.png)

4\. `Deploy`ボタンをクリックしましょう。

Vercel は GitHub と連動しているので,GitHub が更新されるたびに自動でデプロイを行ってくれます。

しばらくしてビルドが完了すると  
下記のように, メッセージとホーム画面が出力されます。

![](/public/images/AVAX-messenger/section-4/4_1_4.png)

ホーム画面の表示部分はリンクになっているので, クリックするとあなたの作成した dapp がブラウザで確認できます 🎉

### 🙋‍♂️ 質問する

ここまでの作業で何かわからないことがある場合は,Discord の `#messenger-dapp` で質問をしてください。

ヘルプをするときのフローが円滑になるので,エラーレポートには下記の 3 点を記載してください ✨

```
1. 質問が関連しているセクション番号とレッスン番号
2. 何をしようとしていたか
3. エラー文をコピー&ペースト
4. エラー画面のスクリーンショット
```

### 🎫 NFT を取得しよう!

NFT を取得する条件は,以下のようになります。

1. MVP の機能がすべて実装されている（実装 OK）

2. Web アプリケーションで MVP の機能が問題なく実行される（テスト OK）

3. このページの最後にリンクされている Project Completion Form に記入する

4. Discord の `messenger-dapp` チャンネルに,あなたの Web サイトをシェアしてください 😉🎉 Discord に投稿する際に,追加実装した機能とその概要も教えていただけると幸いです!

プロジェクトを完成させていただいた方には,NFT をお送りします。

### 🎉 おつかれさまでした!

あなたは,コントラクトを Avalanche C-Chain へデプロイし,コントラクトと通信する Web アプリケーションを立ち上げました。

この dapp プロジェクトが, 現在多くの開発者の注目を集める Avalanche というプラットフォームを理解する手助けになれば幸いです。

これからも Web3 への旅を一緒に楽しみましょう 🚀
