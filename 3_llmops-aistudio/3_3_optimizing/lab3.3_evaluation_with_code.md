---
レイアウト: デフォルト
タイトル: Lab 3.3.2 groundness evaluation using Prompt Flow (Code)
固定リンク: /3_3_2_evaluation/
親: ラボ 3.3 の概要
grand_parent:ラボ3。Azure AI Studio を使用した SLM の LLMOps
nav_order:632
---

# ラボ 3.3.2 プロンプトフローを用いた根拠性評価 (コード)

![LLM
](images/evaluation-monitor-flow.png)
[ジェネレーティブAIアプリケーションの評価とモニタリング](https://learn.microsoft.com/en-us/azure/ai-studio/concepts/evaluation-approach-gen-ai#evaluating-and-monitoring-of-generative-ai-applications)

### 前提 条件

- AI Hub と AI プロジェクト リソースを作成できる Azure サブスクリプション
- Azure AI Studio にデプロイされた gpt-4o モデル


### タスク

- モデルが疑問にどれだけうまく答えているかを定量的に検証したい 
- 本番環境で一括データをベンチマークし、ボトルネックを見つけて改善したい 


### 目次
    1️⃣ Execute batch run to get the base run data 
    2️⃣ Evaluate the "groundedness" by your evalution flow


### Jupyter Notebookを通じて作業
- promptflow python sdkを使用して、jupyter notebookでgroundness評価フローを作成して実行してみましょう。Azure [promptflow_with_evaluation_code.ipynb でフローを評価する方法を学習します](promptflow_with_evaluation_code.ipynb)
