# sharebalance

ポーカー仲間の収支を共有するためのシンプルなWebアプリ。

## セットアップ必須項目

### 1. 合言葉の設定

`index.html` の `PASSCODE_SHA256` を自分たちの合言葉のSHA-256ハッシュに書き換えてください。

ターミナルで生成する例:

```bash
echo -n "あなたの合言葉" | shasum -a 256 | awk '{print $1}'
```

ブラウザのDevToolsで生成する例:

```js
const t = new TextEncoder().encode("あなたの合言葉");
crypto.subtle.digest('SHA-256', t).then(b =>
  console.log(Array.from(new Uint8Array(b)).map(x=>x.toString(16).padStart(2,'0')).join(''))
);
```

出力された64文字の16進文字列を `PASSCODE_SHA256` に貼り付け。

### 2. Firebase 認証の有効化

Firebase Console → Authentication → Sign-in method → **匿名** を「有効」にする。

### 3. Firestore セキュリティルールの適用

Firebase Console → Firestore Database → ルール、に `firestore.rules` の内容を貼り付けて「公開」。

または Firebase CLI で:

```bash
firebase deploy --only firestore:rules
```

## セキュリティモデル

- 合言葉はクライアント側（JavaScript内のハッシュ）で照合するため、**ソースコードを読めるユーザーには総当たり攻撃が理論上可能**です。推測困難な合言葉を選んでください。
- Firestoreルールにより、匿名認証を経ていないアクセスは拒否されます。ただし匿名認証自体は誰でも呼び出し可能なため、**真の意味でメンバー限定にするには Google 認証 + 許可メアドリスト** への変更を推奨します。
- 本アプリは「ポーカー仲間の軽量な共有」を想定した設計です。機密性の高い用途には使わないでください。
