# Synthetic Taxi - 論文再現実験

## プロジェクト概要
論文「A Systematic Evaluation of Generative Models on Tabular Transportation Data (arXiv:2502.08856)」の再現実験

## 進め方
- `docs/spec.md` — 実験仕様（全体の手順・成功判定基準）
- `docs/progress.md` — 現在の進捗状況（何が完了し、何が残っているか）
- `docs/tasks/` — フェーズ別タスク定義

作業再開時は必ず `docs/progress.md` を確認し、次のアクションから着手すること。

## 環境
- Python仮想環境: `.venv/` (Python 3.11, uv管理)
- 論文リポジトリ: `transportation/` (clone済み)
- 実験スクリプト: `transportation/tab_main.py`
- 実験結果: `transportation/output/`

## 実行方法
```bash
cd transportation
/Users/hatanotakuma/works/synthetic_taxi/.venv/bin/python tab_main.py \
  --dataset green --nums 40000 --methods <モデル名> --outfile <出力名> --nexp 3 --ngen 5
```

## 注意事項
- エージェント並行実行時は `--outfile` を分けて出力ファイルの競合を避ける
- STaSyはOOMリスクあり、失敗時はスキップ可
- `settings.local.json` で `Bash(*)` を許可済み
