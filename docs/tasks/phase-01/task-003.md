# Task 003: スモークテスト

## 概要
GaussianCopulaで1000件の小規模テストを実行し、パイプラインの動作確認

## 手順
```bash
python tab_main.py --dataset green --nums 1000 --methods GaussianCopula --outfile smoke_test
```

## 完了条件
- エラーなく完了
- 合成データが生成される
