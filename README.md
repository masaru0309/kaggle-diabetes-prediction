# Kaggle - Playground Series: Diabetes Prediction

<!-- ここにバッジ（技術タグ）を入れるとエンジニアっぽくてカッコいい！ -->
![Python](https://img.shields.io/badge/Python-3.12.12-blue)
![LightGBM](https://img.shields.io/badge/Model-LightGBM-orange)

## 1. Project Overview (プロジェクト概要)
本リポジトリは、Kaggle「Diabetes Prediction Challenge (Playground Series s5e12)」における解法コードです。最終的にrivate leaderboardで4208人中1236位(上位約29.4%)の成績を収めました。
患者の健康データから糖尿病の発症有無を予測する二値分類タスク（評価指標：ROC-AUC）において、EDA（探索的データ解析）に基づく特徴量エンジニアリングとLightGBMのチューニングを実施しました。開発プロセスでは、AIを単なるコード生成ツールとしてではなく「協働のパートナー」として活用しています。自らの仮説に基づいてEDAのグラフ描画を指示し、対話を通じて新規特徴量を考案するなど、主体的に分析を主導しました。
* 当該 Kaggle competition URL : https://www.kaggle.com/competitions/playground-series-s5e12

## 2. Repository Structure (ファイル構成)
スコア改善のプロセスが順を追って分かるよう、以下の3つのノートブックで構成しています。

* [01_baseline.ipynb]()
Private Score:0.63605
  * データの基本情報確認（欠損値・異常値・データ型のチェック）と、ロジスティック回帰による初期ベースライン作成。
* [02_feature_engineering.ipynb]
Private Score:0.69438
  * 各特徴量における目的変数（糖尿病の有無）の分布を可視化し、新規特徴量（例：BMIと血圧の掛け合わせ等）を追加して精度向上を検証。モデルはLightGBMを使用。
* [03_final_model_tuning.ipynb]
Private Score:0.69511
  * 最も効いた特徴量セットに対し、ハイパーパラメータ調整（モデルチューニング）を行い、予測を出力。これを最終提出モデルとした。
* [04_failed_hyperparameter_tuning.ipynb]
Private Score:0.69478
  * さらなる精度向上を狙い EARLY_STOPPING = 200 等の過度なパラメータ調整を試みた実験記録。過学習となり、Private Scoreが`03_final_model_tuning.ipynb`を下回った。

## 3. Solution Approach (解法アプローチ)

### Feature Engineering (特徴量エンジニアリング)
本プロジェクトでは、各特徴量における目的変数の分布変化を観察し、医学知識に落とし込むアプローチを重視しました。
* ノイズの除去
  * 予測に寄与しない、あるいは悪影響を与える可能性のあるID列を初期段階で除外。
* 血圧データからの新規特徴量抽出（脈圧・平均血圧）
  * 既存の特徴量 systolic_bp（収縮期血圧）の分布グラフを分析した結果、血圧が上昇するにつれて非糖尿病（0）の割合が減少し、糖尿病（1）との乖離が顕著に拡大する傾向を発見しました。
  * この考察をもとに、diastolic_bp（拡張期血圧）も組み合わせて**「脈圧（Pulse Pressure）」および「平均血圧（Mean Arterial Pressure）」**という実質的な健康指標を表す新規特徴量を作成し、モデルの予測力を底上げしました。

### Validation Strategy (検証設計)
* **Stratified K-Fold (k=5)** を採用。目的変数（糖尿病の有無）の割合が学習用と検証用で一致するように分割し、手元のCVスコアとPublicスコアの乖離（過学習）を防ぎながら実験を行いました。

### Model Tuning (ハイパーパラメータの最適化)
* 特徴量エンジニアリングで構築したデータセットに対し、LightGBMのハイパーパラメータ調整を実施。特に、learning_rate(0.02) や num_leaves(64) などを調整して学習精度を向上しつつ、過学習を防ぐために min_data_in_leaf(50) などのパラメータを調整し、バランスを慎重に探りました。

## 4. Learnings & Future Work
* 「公式」に囚われない特徴量エンジニアリングの重要性  
  * EDAの段階で、コレステロール値等において明確な分布の乖離を確認していたものの、「脈圧」のような医学的公式が存在する特徴量の作成に留まってしまいました。上位解法を学び、単純な四則演算（例: tc_hdl等）による「名もなき特徴量」が予測に寄与することを知り、自分の発想が既存の枠に囚われていたことに気づきました。今後は純粋なデータ駆動のアプローチも柔軟に取り入れたいと考えています。

## 5. Results (結果)
* **CV Score (ROC-AUC):** 0.〇〇〇〇 (※手元の最高スコア)
* **Public LB:** 0.69753
* **Private LB:** 0.69511
* **Final Rank:** 上位29%
