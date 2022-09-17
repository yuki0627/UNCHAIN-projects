### 送金機能に必要な要件

エアドロップ機能が完成しましたので、ウォレットに入った `SOL` の使い道を考えてみましょう。

実際のアプリケーションでは、`SOL` を他の暗号通貨と交換したり、商品やサービスの代金の支払いに使ったり、あるいは誰かにSOLを贈ったりししたいですね。そのためには、自分のウォレットから他のウォレットに暗号通貨を送金するよう、ネットワーク台帳に知らせる方法が必要です。

送金機能を作るうえで必要な情報はとして、送り手と受け手の2つのアドレスを受け取る関数が必要になりそうですね。また、送金する `SOL` または `Lamport` の数も情報として必要となります。

一方で、重要な要素があります。それは、あなたが実際にその暗号通貨の所有者であり、送金を承認していることをネットワークに証明しなければならないということです。

![](/public/images/304-Solana-Wallet/section-3/3_2_4.png)

大家さんへ家賃の支払いに使うような、伝統的な紙の小切手で考えてみるとわかりやすいかもしれませんね。

小切手の左上には、あなたの名前と住所が印刷されています。また、受取人の名前を記入する欄があり、支払金額を記入する欄もあります。最後に、あなたが送金を承認していることを銀行に証明するために、小切手に署名する欄があります。

ブロックチェーンの送金もかなり似ています。ネットワークがその取引を有効であると確認し、対応する残高を変更できるように、取引に署名する方法が必要です。署名は送信者を認証し、受信者に対してはメッセージが漏洩していないことを証明します。

これらの構成要素を念頭に置いて、送金機能をつくっていきましょう!

### 導入

送金をするために、トランザクション・オブジェクトを構築して送信する必要があります。

ドキュメントを参照してみると、トランザクションを送信する方法は2つあることがわかります。

- `Connection` の `sendTransaction` メソッドと
- 一般的な関数 `sendAndConfirmTransaction`

トランザクションを送信したあと、さらに **残高を更新するために確認を要求したい** ので、２つ目を試してみるのが良さそうです。

![](/public/images/304-Solana-Wallet/section-3/3_2_1.png)

この関数の仕様を読むと、

- `Connection`
- `Transaction` オブジェクト
- 署名者の配列

の3つのパラメーターを用意する必要がありそうです。さらに、この関数は **「トランザクションに署名し、送信し、確認する」** と書かれているので、これからつくりたい機能に適しているように思います。

まずはこの関数をインポートし、次に関数呼び出しに必要な引数を用意していきましょう。

```javascript
// ファイルの先頭で sendAndConfirmTransaction 関数を読み込む
import { sendAndConfirmTransaction } from "@solana/web3.js";

// これまでと同じように connection インスタンスを作成する
const NETWORK = 'devnet';
const connection = new Connection(clusterApiUrl(NETWORK), "confirmed");

// sendAndConfirmTransaction 関数を呼び出す
// connection はすでに用意しているので、transaction と signers をこれから用意していきます
const confirmation = await sendAndConfirmTransaction(
  connection,
  transaction,
  signers
);
```

### Transaction

すでに `connection` はできているので、次に `transaction` を作成していきましょう。

ドキュメントにある[Transactionクラスへのリンク](https://solana-labs.github.io/solana-web3.js/classes/Transaction.html)をたどると、`Transaction` オブジェクトを作成するには、そのコンストラクタを使用することができるようです。

```javascript
// サンプルコード
const transaction = new Transaction();

console.log(transaction);
// > Transaction {signatures: Array(0), feePayer: undefined, instructions: Array(0), recentBlockhash: undefined, nonceInfo: undefined}
```

作成したトランザクションを見ると、送信者、受信者、金額といった有効なトランザクションに必要な構成要素が明らかに欠けていますが、入力できるはずの使い慣れた構造を持っているようです。

それぞれを調査することもできますが、手始めとして有望だと思われる `instructions` プロパティを調べてみましょう。

ドキュメントには直感的な進め方が書かれていないのが残念ですが、[`SystemProgram`](https://solana-labs.github.io/solana-web3.js/classes/SystemProgram.html) という便利そうなクラスに `transfer` メソッドがあり、 **「ある口座から別の口座にlamportsを移す取引命令を生成する」** と書かれています。これはまさに今必要としているメソッドのようですね!

![](/public/images/304-Solana-Wallet/section-3/3_2_2.png)

### Transfer

`transfer` メソッドは `TransferParams` オブジェクトを受け取ります。

そして `TransferParams` は

- 送信者
- 受信者
- 金額(lamports)

この3つで構成されています。

![](/public/images/304-Solana-Wallet/section-3/3_2_5.png)

これは私たちが取引に使いたいデータと一致していますね!

ですので、 `instructions` は次のように組み立てることができます。

```javascript
// サンプルコード
const instructions = SystemProgram.transfer({
  fromPubkey: account.publicKey,
  toPubkey: new PublicKey("受信者のアドレス"),
  lamports: LAMPORTS_PER_SOL,
});
```

受信者のアドレスは `Html Form` から文字列で受け取ることを想定していますが、`toPubkey` プロパティでは `PublicKey`型を想定しているため、受信者用の `PublicKey` をインスタンス化しなければなりません。

これらを `Transaction` に組み込むには、addメソッドを使用します。

```javascript
transaction.add(instructions);
```

あるいは、ちょっとしたリファクタリングで、 `instructions` を作成した後に `Transaction` をインスタンス化し、すぐに追加することもできますね!

```javascript
const transaction = new Transaction().add(instructions);
```

`sendAndConfirmTransaction` 関数の3つのパラメータのうち、2つ（`connection` と `transaction` ）を用意しました

### 署名

次に、`signers` の配列が必要です。この関数の仕様から、`signers` は少なくとも1つの `Signer` オブジェクトを含む配列になることが分かっています。[ドキュメント](https://solana-labs.github.io/solana-web3.js/interfaces/Signer.html)で `Signer` の型を確認すると、`publicKey`と `secretKey` という2つのプロパティを持つオブジェクトのようです。

![](/public/images/304-Solana-Wallet/section-3/3_2_3.png)

`account` から両方を取得できるので、`signers` の配列を作成することができます。

```javascript
const signers = [
  {
    publicKey: account.publicKey,
    secretKey: account.secretKey,
  },
];
```

### 送信と確認

これで3つのパラメータがすべて完了したので、最後に `sendAndConfirmTransaction` を呼び出して、その確認を待つことができるようになりました。

```javascript
const confirmation = await sendAndConfirmTransaction(
  connection,
  transaction,
  signers
);
```

これで、`Solana` アカウント間で暗号通貨を移動することができる機能が完成しました。この機能を完成させるには、送金後にアカウントの残高を更新するために `refreshBalance` 関数を呼び出す必要があります。

```javascript
await refreshBalance();
```

### 送金機能を完成させよう

以上を踏まえて、 送金機能を完成させていきましょう!

まず、必要な関数やクラスをインポートします。

```javascript
import { Keypair, Connection, clusterApiUrl, LAMPORTS_PER_SOL, SystemProgram, PublicKey, Transaction, sendAndConfirmTransaction } from "@solana/web3.js";
```

送金処理を行う `handleTransfer` 関数を定義し、中身を書いていきましょう。

```javascript
const handleTransfer = async (e) => {
  e.preventDefault();

  // 受信者のアドレスはフォームに入力された値を使用
  const toAddress = e.target[0].value;
  console.log('toAddress', toAddress);

  try {
    console.log('送金中...')
    setTransactionSig("");

    const connection = new Connection(clusterApiUrl(NETWORK), "confirmed");

    const instructions = SystemProgram.transfer({
      fromPubkey: account.publicKey,
      toPubkey: new PublicKey(toAddress),
      lamports: LAMPORTS_PER_SOL,
    });

    const transaction = new Transaction().add(instructions);

    const signers = [
      {
        publicKey: account.publicKey,
        secretKey: account.secretKey,
      },
    ];

    const confirmation = await sendAndConfirmTransaction(
      connection,
      transaction,
      signers
    );
    console.log('confirmation', confirmation);

    setTransactionSig(confirmation);

    await refreshBalance();

    console.log('送金が完了しました!!!')
  } catch (error) {
    console.log('error', error);
  }
};
```

そして、受信者アドレスを入力するフォームと、送金ボタンをレンダリングします。
ついでに、実際のトランザクションをあとで確認できるように、送金完了したら `Solana Explorer` へのリンクを表示してあげると良さそうです!

```javascript
<div>
  <h2 className="p-2 border-dotted border-l-4 border-l-indigo-400">STEP5: 送金機能を実装する</h2>
  {account && (
    <>
      <form onSubmit={handleTransfer} className="my-6">
        <div className="flex items-center border-b border-indigo-500 py-2">
          <input
            type="text"
            className="w-full text-gray-700 mr-3 p-1 focus:outline-none"
            placeholder="送金先のウォレットアドレス"
          />
          <input
            type="submit"
            className="p-2 text-white bg-indigo-500 focus:ring focus:ring-indigo-300 rounded-lg cursor-pointer"
            value="送金"
          />
        </div>
      </form>
      {transactionSig && (
        <>
          <span className="text-red-600">送金が完了しました!</span>
          <a
            href={`https://explorer.solana.com/tx/${transactionSig}?cluster=${NETWORK}`}
            className="border-double border-b-4 border-b-indigo-600"
            target='_blank'
          >
            Solana Block Explorer でトランザクションを確認する
          </a>
        </>
      )}
    </>
  )}
</div>
```

### ✅ 動作確認

おめでとうございます!これで送金機能が完成しました。

実際に送金して、相手のウォレットの残高が増えているかを確認してください。

自分ひとりで二つのウォレットを同時に作成＆表示したい場合は、もう１つターミナルを立ち上げて `npm run dev` を実行します。

そうすると、別のポートでアプリケーションが起動されますので、そちらでウォレットの作成をし、アドレスをコピーして１つ目のウォレットから送金を試してみる方法がおすすめです!

※今回の実装では、送金するSOLは `1 SOL` で固定となっていますが、送金する際にガス代と呼ばれる手数料が必要になるため、残高がちょうど `1 SOL` だと、送金が失敗します。そういったエラーが発生することも確かめつつ、残高を `2 SOL` などにしてから送金を試してみてくださいね🥭

### 📝 このセクションで追加したコード

```diff
 import { useState } from "react";
-import { Keypair, Connection, clusterApiUrl, LAMPORTS_PER_SOL } from "@solana/web3.js";
+import { Keypair, Connection, clusterApiUrl, LAMPORTS_PER_SOL, SystemProgram, PublicKey, Transaction, sendAndConfirmTransaction } from "@solana/web3.js";
 import * as Bip39 from "bip39";

 const NETWORK = 'devnet';
@@ -8,6 +8,7 @@ export default function Home() {
   const [mnemonic, setMnemonic] = useState(null);
   const [account, setAccount] = useState(null);
   const [balance, setBalance] = useState(null);
+  const [transactionSig, setTransactionSig] = useState("");

   const generateWallet = () => {
     const generatedMnemonic = Bip39.generateMnemonic();
@@ -61,6 +62,50 @@ export default function Home() {
     }
   };

+
+  const handleTransfer = async (e) => {
+    e.preventDefault();
+    const toAddress = e.target[0].value;
+    console.log('toAddress', toAddress);
+
+    try {
+      console.log('送金中...')
+      setTransactionSig("");
+
+      const connection = new Connection(clusterApiUrl(NETWORK), "confirmed");
+
+      const instructions = SystemProgram.transfer({
+        fromPubkey: account.publicKey,
+        toPubkey: new PublicKey(toAddress),
+        lamports: LAMPORTS_PER_SOL,
+      });
+
+      const transaction = new Transaction().add(instructions);
+
+      const signers = [
+        {
+          publicKey: account.publicKey,
+          secretKey: account.secretKey,
+        },
+      ];
+
+      const confirmation = await sendAndConfirmTransaction(
+        connection,
+        transaction,
+        signers
+      );
+      console.log('confirmation', confirmation);
+
+      setTransactionSig(confirmation);
+
+      await refreshBalance();
+
+      console.log('送金が完了しました!!!')
+    } catch (error) {
+      console.log('error', error);
+    }
+  };
+
   return (
     <div className="p-10">
       <h1 className="text-5xl font-extrabold tracking-tight text-gray-900">
@@ -156,6 +201,36 @@ export default function Home() {

       <div>
         <h2 className="p-2 border-dotted border-l-4 border-l-indigo-400">STEP5: 送金機能を実装する</h2>
+        {account && (
+          <>
+            <form onSubmit={handleTransfer} className="my-6">
+              <div className="flex items-center border-b border-indigo-500 py-2">
+                <input
+                  type="text"
+                  className="w-full text-gray-700 mr-3 p-1 focus:outline-none"
+                  placeholder="送金先のウォレットアドレス"
+                />
+                <input
+                  type="submit"
+                  className="p-2 text-white bg-indigo-500 focus:ring focus:ring-indigo-300 rounded-lg cursor-pointer"
+                  value="送金"
+                />
+              </div>
+            </form>
+            {transactionSig && (
+              <>
+                <span className="text-red-600">送金が完了しました!</span>
+                <a
+                  href={`https://explorer.solana.com/tx/${transactionSig}?cluster=${NETWORK}`}
+                  className="border-double border-b-4 border-b-indigo-600"
+                  target='_blank'
+                >
+                  Solana Block Explorer でトランザクションを確認する
+                </a>
+              </>
+            )}
+          </>
+        )}
       </div>
     </div>
   )
```

### ☕️ 豆知識

Solana Explorerでは、特定のブロック、アカウント、トランザクション、コントラクト、トークンをネットワーク単位で検索することができます。イーサリアムでは `Ethersacn`, BSC では `BscScan` が有名ですね🥭

### 🙋‍♂️ 質問する

ここまでの作業で何かわからないことがある場合は、Discord の `#section-3` で質問をしてください。

ヘルプをするときのフローが円滑になるので、エラーレポートには下記の 3 点を記載してください ✨

```
1. 質問が関連しているセクション番号とレッスン番号
2. 何をしようとしていたか
3. エラー文をコピー&ペースト
4. エラー画面のスクリーンショット
```


おめでとうございます✨送金機能が完成しました!
次のレッスンでは、Vercel に WEB アプリをデプロイしていきます💪
