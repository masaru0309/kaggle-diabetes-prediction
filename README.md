# 🏆 Kaggle - Playground Series: Diabetes Prediction

<!-- ここにバッジ（技術タグ）を入れるとエンジニアっぽくてカッコいい！ -->
![Python](https://img.shields.io/badge/Python-3.12.12-blue)
![LightGBM](https://img.shields.io/badge/Model-LightGBM-orange)

## 📌 1. Project Overview (プロジェクト概要)
本リポジトリは、Kaggle「Diabetes Prediction Challenge (Playground Series s5-e12)」における解法コードです。最終的にrivate leaderboardで4208人中1236位(上位約29.4%)の成績を収めました。
患者の健康データから糖尿病の発症有無を予測する二値分類タスク（評価指標：ROC-AUC）において、EDA（探索的データ解析）に基づく特徴量エンジニアリングとLightGBMのチューニングを実施しました。開発プロセスでは、AIを単なるコード生成ツールとしてではなく「協働のパートナー」として活用しています。自らの仮説に基づいてEDAのグラフ描画を指示し、対話を通じて新規特徴量を考案するなど、主体的に分析を主導しました。

## 📂 2. Repository Structure (ファイル構成)
スコア改善のプロセスが順を追って分かるよう、以下の3つのノートブックで構成しています。

* `01_baseline.ipynb`
  * データの基本確認（欠損値・型のチェック）と、LightGBMデフォルトパラメータによる初期ベースライン作成。
* `02_feature_engineering.ipynb`
  * 各変数と目的変数の関係性を分析し、新規特徴量（例：BMIと血圧の掛け合わせ等）を追加して精度向上を検証。
* `03_final_model_tuning.ipynb`
  * 最も効いた特徴量セットに対し、ハイパーパラメータ調整（チューニング）を行い、最終的な予測を出力。

## 🧠 3. Solution Approach (解法アプローチ)

### Validation Strategy (検証設計)
* **Stratified K-Fold (k=5)** を採用。ターゲット変数（糖尿病の有無）の割合が学習用と検証用で一致するように分割し、手元のCVスコアとPublic LBスコアの乖離（過学習）を防ぎながら実験を管理しました。

### Feature Engineering (特徴量エンジニアリング)
* **〇〇特徴量:** データ探索の結果、〇〇と〇〇に強い相関が見られたため、これらを掛け合わせた特徴量を生成。
* **〇〇の除外:** 初期検証で悪影響を与えていたID列を除外。

## 📊 4. Results (結果)
* **CV Score (ROC-AUC):** 0.〇〇〇〇 (※手元の最高スコア)
* **Public LB:** 0.69753
* **Private LB:** 0.69511
* **Final Rank:** 上位29%
