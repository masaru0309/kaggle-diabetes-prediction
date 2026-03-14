# Kaggle - Playground Series: Diabetes Prediction

<!-- ここにバッジ（技術タグ）を入れるとエンジニアっぽくてカッコいい！ -->
![Python](https://img.shields.io/badge/Python-3.12.12-blue)
![LightGBM](https://img.shields.io/badge/Model-LightGBM-orange)

## 1. Project Overview (プロジェクト概要)
本リポジトリは、Kaggle「Diabetes Prediction Challenge (Playground Series s5e12)」における解法コードです。最終的にPrivate leaderboardで4208人中1236位(上位約29.37%)の成績を収めました。
患者の健康データから糖尿病の発症有無を予測する二値分類タスク（評価指標：ROC-AUC）において、EDA（探索的データ解析）に基づく特徴量エンジニアリングとLightGBMのチューニングを実施しました。開発プロセスでは、AIを単なるコード生成ツールとしてではなく「協働のパートナー」として活用しています。自らの仮説に基づいてEDAのグラフ描画を指示し、対話を通じて新規特徴量を考案するなど、主体的に分析を主導しました。
* 当該 Kaggle competition URL : https://www.kaggle.com/competitions/playground-series-s5e12

## 2. Repository Structure (ファイル構成)
スコア改善のプロセスが順を追って分かるよう、以下の3つのノートブックで構成しています。

* [01_baseline.ipynb](./01_baseline.ipynb)  
Private Score:0.63605
  * データの基本情報確認（欠損値・異常値・データ型のチェック）と、ロジスティック回帰による初期ベースライン作成。
* [02_feature_engineering.ipynb](./02_feature_engineering.ipynb)  
Private Score:0.69438
  * 各特徴量における目的変数（糖尿病の有無）の分布を可視化し、新規特徴量を追加して精度向上を検証。モデルはLightGBMを使用。
* [03_final_model_tuning.ipynb](./03_model_tuning.ipynb)  
Private Score:0.69511
  * 最も効いた特徴量セットに対し、ハイパーパラメータ調整（モデルチューニング）を行い、予測を出力。これを最終提出モデルとした。

## 3. Solution Approach (解法アプローチ)

### Feature Engineering (特徴量エンジニアリング)
本プロジェクトでは、各特徴量における目的変数の分布変化を観察し、医学知識に落とし込むアプローチを重視しました。
* 無関係な特徴量の除去
  * 予測に寄与しない、あるいは悪影響を与える可能性のあるID列を初期段階で除外。
* 血圧データからの新規特徴量抽出（脈圧・平均血圧）
  * 既存の特徴量 systolic_bp（収縮期血圧）の分布グラフを分析した結果、血圧が上昇するにつれて非糖尿病（0）の割合が減少し、糖尿病（1）との乖離が顕著に拡大する傾向を発見しました。  
この考察をもとに、diastolic_bp（拡張期血圧）も組み合わせて**「脈圧（Pulse Pressure）」および「平均血圧（Mean Arterial Pressure）」**という実質的な健康指標を表す新規特徴量を作成し、モデルの予測力を底上げしました。

### Validation Strategy (検証設計)
* **Stratified K-Fold (k=5)** を採用。目的変数（糖尿病の有無）の割合が学習用と検証用で一致するように分割し、手元のCVスコアとPublic LBスコアの乖離（過学習）を防ぎながら実験を行いました。

### Model Tuning (ハイパーパラメータの最適化)
* 特徴量エンジニアリングで構築したデータセットに対し、LightGBMのハイパーパラメータ調整を実施。特に、learning_rate(0.02) や num_leaves(64) などを調整して学習精度を向上しつつ、過学習を防ぐために min_data_in_leaf(50) などのパラメータを調整し、バランスを慎重に探りました。

## 4. Learnings & Future Work

* 「公式」に囚われない特徴量エンジニアリングの重要性  
  * [02_feature_engineering.ipynb](./02_feature_engineering.ipynb)にてEDAの段階で、systolic_bp や bmi, cholesterol_total 等のヒストグラムを確認した際、「横軸の値が増加する（説明変数の数値が高くなる）につれて、非糖尿病患者数（0）と糖尿病患者数（1）の差が激しくなっていく」という明確な傾向を視覚的に捉えていました。しかし、実際に作成した特徴量は、それらのうち systolic_bp 等のみを使用し、「脈圧」や「平均血圧」といった医学的根拠に基づく既存の公式が存在するものを作ることに留まってしまいました。同様に乖離が激しかった bmi や cholesterol_total に関しては、当てはまる公式が思いつかなかったため特徴量作成には使用しませんでした。  
そのコンペ終了後、例えば 「総コレステロール値（cholesterol_total）をHDLコレステロール値（hdl_cholesterol）で割った比率」といった、単純な四則演算から生まれる「医学的な名前が付けられない特徴量」であっても、機械学習の予測精度には大きく寄与することを知りました。  
自分の発想が「意味のある名前がつくものしか特徴量にしてはいけない」という無意識な先入観に囚われていたことに気づき、今後はより柔軟に特徴量を作成していきたいと考えています。

## 5. Results (結果)
* **CV Score (ROC-AUC):** 0.72768
* **Public LBスコア:** 0.69753
* **Private LBスコア:** 0.69511
* **Private leaderboard 最終順位:** 4208人中1236位(上位約29.37%)
