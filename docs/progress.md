# 再現実験 進捗状況

**論文**: A Systematic Evaluation of Generative Models on Tabular Transportation Data (arXiv:2502.08856)
**最終更新**: 2026-04-05

---

## Phase 1: 環境構築・準備 — 完了

### Task 001: 環境構築 — 完了
- Python 3.11.14 (uv venv)
- PyTorch 2.11.0 (MPS利用可能)
- TensorFlow 2.21.0
- SDV, sdmetrics, copulas, deepecho, rdt, ctgan 等 全86パッケージ
- 追加インストール: psutil, tensorboard, rtdl (--no-deps)

### Task 002: リポジトリ取得・データ確認 — 完了
- `transportation/` にclone済み
- greenデータセット: `2015_green_40000.csv` (6.8MB) — 本実験に十分
- zoneデータセット: `tracrData/green_tripdata_2019-03.parquet` (10MB) — グラフ評価用

### Task 003: スモークテスト — 完了
- GaussianCopula, green, 1000件で動作確認済み
- 適用した修正:
  - `tab_main.py`: `import setGPU` をコメントアウト (macOS非対応)
  - 追加パッケージ: psutil, tensorboard, rtdl
- 結果 (GaussianCopula, N=1000):
  - Downstream R²: tr_te=87.77, syn_te=84.70
  - SDV Score: tr_syn=76.13, te_syn=74.94
  - Wasserstein: tr_syn=2.20, te_syn=2.18
  - Coverage: tr_syn=1.16%, te_syn=1.56%
  - rDCR: 1.013
  - 実行時間: 約0.54分

---

## Phase 2: 本実験 — 進行中

### Task 004: Green全モデル実験 (40000件) — 一部完了

| モデル | 状態 | 備考 |
|---|---|---|
| GaussianCopula | **完了** | Green N=40k完了 (syn_te R²=80.46, cov=0.57%, 130分)。zone側も完了 |
| CTGAN | **完了** | nexp=3, ngen=5。syn_te R²=62.32, SDV=82.52, 52.1分 |
| TVAE | **完了** | nexp=3, ngen=5。syn_te R²=82.92, SDV=81.60, 38.6分 |
| CTABGAN | **スキップ** | ユーザー判断によりスキップ |
| TabDDPM | **スキップ** | ユーザー判断によりスキップ |
| STaSy | **スキップ** | ユーザー判断によりスキップ |

### Task 005: グラフ評価 (Zone) — 一部完了
- GaussianCopula のみ完了
  - G_tr_syn=28.86, G_te_syn=27.23 (グラフ類似度)
  - Downstream R²: tr_syn=83.29, syn_te=90.79
- 残り5モデル未実行

### Task 006: 複数シード実行 — 未着手

---

## Phase 3: 結果集計・レポート — 未着手

### Task 007: 結果集計・レポート作成 — 未着手

---

## 既知の問題・対応

| 問題 | 対応状況 |
|---|---|
| `import setGPU` エラー | コメントアウト済み |
| rtdl の torch>=2 依存衝突 | `--no-deps` でインストール済み |
| settings.local.json の権限不足 | `Bash(*)` に修正済み |
| STaSy OOMリスク | 発生時はスキップ方針 |
| TensorFlow依存衝突 | 現状問題なし (TF 2.21.0共存) |

---

## 出力ファイル一覧

| ファイル | 内容 |
|---|---|
| `output/smoke_test_syn_total_amount_42_green.csv` | スモークテスト結果 |
| `output/zone_graph_syn_total_amount_42_zone.csv` | zone GaussianCopula結果 |
| `output/speed.csv` | 実行速度ログ |
| `output/dcr.csv` | プライバシー指標ログ |
| `output/ctgan_green_syn_total_amount_42_green.csv` | CTGAN green結果 |
| `output/tvae_green_syn_total_amount_42_green.csv` | TVAE green結果 |
| `output/gc_green_syn_total_amount_42_green.csv` | GaussianCopula green本実験結果 |

---

## 次のアクション

1. Zone グラフ評価 (CTGAN, TVAE) — 必要に応じて実行
2. 複数シード実行 (seed 1-5) — 必要に応じて実行
3. 結果集計・レポート作成
