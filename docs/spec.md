# 目的

論文
「A Systematic Evaluation of Generative Models on Tabular Transportation Data（arXiv:2502.08856）」
の実験パイプラインを再現する。

目的は以下とする：

* 全モデルの実行
* 合成データの生成
* 評価指標の算出
* 論文の傾向（例：TabDDPMが最もバランスが良い）の確認

※ 数値の完全一致は目的としない

---

# 環境構築（macOS + uv）

## 1. プロジェクト作成

```bash
uv init transportation-repro
cd transportation-repro
```

## 2. 仮想環境作成

```bash
uv venv
source .venv/bin/activate
```

## 3. 依存関係インストール

注意：

* まずCPU環境で安定動作させる
* TensorFlowとPyTorchは競合する可能性あり

```bash
uv pip install \
    torch torchvision torchaudio \
    pandas numpy scipy scikit-learn matplotlib seaborn \
    tqdm pyarrow \
    copulas sdv sdmetrics deepecho rdt \
    category-encoders graphviz cloudpickle \
    POT termcolor wget icecream \
    ml-collections tomli-w
```

## 4. TensorFlow（STaSy使用時のみ）

```bash
uv pip install tensorflow
```

---

# リポジトリ取得

```bash
git clone https://github.com/chengenw/transportation.git
cd transportation
```

---

# データセット準備

必要データ：

* green（通常実験用）
* zone（グラフ構造評価用）

確認：

```bash
ls dataset/
```

不足している場合：

* NYC TLC Trip Record Data（Green Taxi）を取得
* 以下の前処理を実施：

  * Ehail fee 削除
  * 日時 → 曜日・時間へ変換
  * カテゴリ＋数値の混在テーブルへ整形

論文設定：

* 学習：40,000件
* テスト：20,000件

---

# Step1：動作確認（必須）

```bash
python tab_main.py \
  --dataset green \
  --nums 1000 \
  --methods GaussianCopula \
  --outfile smoke_test
```

期待結果：

* エラーなく完了
* 出力ディレクトリ生成
* 合成データ生成

---

# Step2：本実験（通常評価）

```bash
python tab_main.py \
  --dataset green \
  --nums 40000 \
  --outfile green_all
```

対象モデル：

* GaussianCopula
* CTGAN
* TVAE
* CTABGAN
* STaSy
* TabDDPM

---

# Step3：グラフ評価

```bash
python tab_main.py \
  --dataset zone \
  --nums 40000 \
  --outfile zone_graph \
  --no_sdv --no_prv
```

※ zoneデータはOD構造評価専用

---

# Step4：複数回実行（重要）

論文条件：

* 学習3回 × サンプリング5回 = 計15回

簡易実装：

```bash
for seed in 1 2 3 4 5; do
  python tab_main.py \
    --dataset green \
    --nums 40000 \
    --seed $seed \
    --outfile green_seed_$seed
done
```

---

# Step5：結果集計

実装内容：

* 出力結果の読み込み
* 平均・標準偏差の算出
* 以下指標の再構成：

  * Downstream task（R²）
  * Wasserstein距離
  * Privacy（DCR ratio）
  * Graph metric

---

# 成功判定（重要）

以下を確認：

1. TabDDPMが総合的に最も性能が良い
2. GaussianCopulaは高速だが品質・プライバシーは弱い
3. 一部モデルでモード崩壊が発生
4. グラフ構造で実データとの差が残る

---

# 既知の問題

1. STaSyでOOM発生の可能性
   → 必要ならスキップ可

2. TensorFlow依存衝突
   → 別環境に分離推奨

3. メモリ負荷（Wasserstein）
   → サンプル数削減で対応可

---

# 拡張（任意）

* Membership Inference Attackの追加
* 分布可視化（ヒストグラム・OD行列）
* 合成データ vs 実データの差分分析

---

# 成果物

以下を出力：

1. 実行ログ要約
2. モデル比較表
3. 簡易分析：

   * 最も良いモデル
   * 失敗パターン
   * 論文との差異

---

# 出力形式

* Markdownレポート
* 表形式（比較可能な形）
* 再現性のある手順説明付き
