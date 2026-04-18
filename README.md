# AR ボクセルエディタ

iPhone のカメラで平らな面（1 m × 1 m の紙）を映し、4×4 グリッドのセルをタップすると半透明のボクセルが積み上がる Web AR アプリです。

## デモ

| 状態 | 説明 |
|---|---|
| 検索中 | カメラで平面を探しています |
| タップして配置 | リングが表示されたらタップしてグリッドを配置 |
| 編集中 | グリッドセルをタップしてボクセルを追加 |

## 使い方

1. **HTTPS** でホストされた `index.html` を iPhone の Safari で開く
2. 「AR を開始」ボタンをタップしてカメラ許可を承認
3. カメラで **平らな紙（1 m × 1 m）** を映す
4. 青いリングが紙の上に表示されたら **タップしてグリッドを配置**
5. グリッドの各セルをタップすると **ボクセルが積み上がる**
6. 同じセルを繰り返しタップすると **ボクセルが縦に積まれる**

### UI ボタン

| ボタン | 機能 |
|---|---|
| ↩ 戻す | 最後に置いたボクセルを1つ削除 |
| 🗑 リセット | 全ボクセルを削除 |
| 再配置 | グリッドをやり直す（ボクセルもリセット） |

## 動作環境

- **iOS 15.4 以降** の Safari（推奨: iOS 17+）
- **Android Chrome**（WebXR AR 対応機種）
- **HTTPS 接続必須**（カメラ・WebXR API の要件）

> HTTP localhost での動作は不可。GitHub Pages / Vercel / Netlify 等の HTTPS ホスティングを利用してください。

## ホスティング例

### GitHub Pages

```bash
# リポジトリの Settings > Pages > Source を「main ブランチ」に設定するだけ
```

### Vercel / Netlify

```bash
# index.html を含むフォルダをそのままデプロイ
```

### ローカル開発（HTTPS）

```bash
# mkcert でローカル証明書を作成
mkcert localhost
npx serve . --ssl-cert localhost.pem --ssl-key localhost-key.pem
```

## 技術スタック

| 技術 | 用途 |
|---|---|
| [Three.js r160](https://threejs.org/) | 3D レンダリング |
| [WebXR API](https://developer.mozilla.org/en-US/docs/Web/API/WebXR_Device_API) | AR セッション・平面検出 |
| ARKit (iOS) / ARCore (Android) | 実空間トラッキング |
| HTML Canvas 2D | タップリップルエフェクト |

## 仕様

| 項目 | 値 |
|---|---|
| グリッドサイズ | 1 m × 1 m |
| セル数 | 4 × 4（1 セル = 25 cm × 25 cm） |
| ボクセルサイズ | 約 21.5 cm × 22.5 cm × 21.5 cm |
| ボクセル透明度 | 72% |
| 列カラー | 青 / 赤 / 緑 / 琥珀（積み上げるほど明るくなる） |

## アーキテクチャ

```
startAR()
  └─ navigator.xr.requestSession('immersive-ar', { hit-test, local })
       └─ onFrame() ループ
            ├─ hit-test → reticle 位置を更新（scanning / placing フェーズ）
            └─ renderer.render(scene, camera)

handleTap()
  ├─ placing フェーズ → placeGrid()（reticle 位置にグリッドを固定）
  └─ editing フェーズ
       ├─ renderer.xr.getCamera().cameras[0] でレイキャスト
       ├─ グリッド平面との交差点を計算
       └─ addVoxel(row, col)
```

## ファイル構成

```
index.html   # アプリ本体（単一ファイル）
```
