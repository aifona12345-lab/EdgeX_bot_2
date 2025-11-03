# Mirror Hedge Bot for EdgeX

参照アカウント（例: アカウントA）のネットポジションに常時追随し、あなたのアカウントのポジションを成行注文のみでミラーヘッジします。`aifona12345-lab/EdgeX_bot` の構成・認可フロー（GAS）・SDK利用方針に準拠しています。

参考リポジトリ: [`EdgeX_bot`](https://github.com/aifona12345-lab/EdgeX_bot)

## セットアップ

1. 依存インストール
   ```bash
   pip install -r requirements.txt
   ```
   ルートの `requirements.txt` は参照リポジトリの依存にリンクしています。

2. 環境変数
   `.env` を作成するか、環境に設定してください（`.env.example` 参照）。

## 環境変数
- 必須
  - `EDGEX_ACCOUNT_ID`: あなたのアカウントID
  - `EDGEX_STARK_PRIVATE_KEY`: あなたの秘密鍵
  - `REF_ACCOUNT_ID`: 参照（ミラー元）アカウントID
  - `REF_STARK_PRIVATE_KEY`: 参照アカウントの秘密鍵（必須）
- 対象銘柄（未指定なら参照アカウントの保有ポジ銘柄を自動検出）
  - `EDGEX_CONTRACT_ID`: 単一銘柄ID（例: `10000001`）
  - または `EDGEX_CONTRACT_ID_LIST`: カンマ区切り（例: `10000001,10000002`）
- 任意（推奨）
  - `EDGEX_BASE_URL`: 既定は `https://pro.edgex.exchange`
  - `MIRROR_POLL_SEC`: ポーリング間隔（既定 3.0）
  - `MIRROR_BAND`: 許容バンド（サイズ、既定 0.0）
  - `MIRROR_STEP_MAX`: 1回の最大追随サイズ（既定 0.0=無制限）
  - `EDGEX_SIZE_STEP`: 取引所メタが取れない場合のサイズ刻み上書き

備考: 起動時に `EdgeX_bot` と同じ GAS(Web) 認証チェックを行います。許可されていないアカウントIDは起動できません。

## 実行
```bash
python run_mirror_hedge.py
```

ログは `logs/run_mirror_hedge.log` に出力されます。

## 挙動
- 参照アカウントと自アカウントのネットポジションを取得し、差分が `MIRROR_BAND` を超えた場合のみ成行注文で乖離を縮小します。
- 数量は取引所メタデータ（サイズ刻み・最小数量）に合わせて丸めます。
- 複数銘柄を指定した場合は順に評価・追随します。

## デプロイ（Koyeb の例）
- 参照リポジトリの Docker 運用を流用する場合、エントリポイントを `python run_mirror_hedge.py` に変更してください。
- 環境変数は本READMEの記載に従い設定します。

## トラブルシュート
- 認証NG: GAS 側の許可リストに `EDGEX_ACCOUNT_ID` が含まれているか確認してください。
- 刻みエラー: 取引所のエラー内容に従い `EDGEX_SIZE_STEP` を設定してください。


