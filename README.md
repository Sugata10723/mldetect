# mldetect

機械学習を用いたIoTデバイスの異常検知システム

## 概要

IoTデバイスから収集したシステムメトリクスデータを機械学習で分析し、異常なデバイスを検出するプロジェクトです。

## 必要な環境

- Python 3.9以上
- macOS

## セットアップ手順

### 1. リポジトリのクローン

```bash
git clone <リポジトリURL>
cd mldetect
```

### 2. 仮想環境の作成と有効化

Pythonの仮想環境を作成することで、プロジェクト専用の独立した環境を構築できます。

```bash
# 仮想環境の作成
python3 -m venv venv

# 仮想環境の有効化
source venv/bin/activate
```

仮想環境が有効化されると、ターミナルのプロンプトに `(venv)` が表示されます。

### 3. 依存パッケージのインストール

仮想環境を有効化した状態で、以下のコマンドを実行します：

```bash
pip install -r requirements.txt
```

このコマンドで、pandas、scikit-learn、matplotlib、Jupyter Notebookなど、必要なすべてのパッケージが自動的にインストールされます。

### 4. データセットの準備

FabIoT-Datasetをプロジェクトディレクトリに配置し、[src/config.py](src/config.py) を編集します：

```python
DATA_PATH = "/path/to/your/FabIoT-Dataset-main/data/"
```

例：
```python
DATA_PATH = "/Users/yourname/mldetect/FabIoT-Dataset-main/data/"
```

### 5. 実行方法

#### 方法A: VSCodeで実行

1. VSCodeで [main.ipynb](main.ipynb) を開く
2. 右上のカーネル選択で、作成した仮想環境の Python インタープリタ（`./venv/bin/python3`）を選択
   - 「カーネルの選択」→「Python環境」→「venv」を選択
3. 各セルを順番に実行（`Shift + Enter` または各セルの左側の「▶」ボタン）
4. すべてのセルを一度に実行する場合は、上部の「すべて実行」ボタンをクリック

#### 方法B: Jupyter Notebookをブラウザで起動

仮想環境を有効化した状態で、以下のコマンドを実行します：

```bash
jupyter notebook
```
表示されたURL（`http://127.0.0.1:8888/?token=...`）をブラウザで開きます。

**ノートブックの実行:**
1. 開いたブラウザで [main.ipynb](main.ipynb) をクリック
2. 各セルを順番に実行（`Shift + Enter` または上部の「▶ Run」ボタン）
3. すべてのセルを一度に実行する場合は、メニューから「Cell」→「Run All」を選択

## 実行内容の詳細

### セル1: ライブラリのインポート
```python
import pandas as pd
from sklearn.linear_model import LogisticRegression
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split
```

**何をしているか:**
- データ分析に必要なライブラリを読み込み
- 機械学習モデル（Random Forest、KNN、線形回帰）をインポート
- データの前処理ツール（標準化、分割）を準備

### セル2: データの読み込みとラベル付け
```python
csv_files = list(folder_path.glob('*/*.csv'))
```

**何をしているか:**
- 指定したフォルダ内のすべてのCSVファイルを検索
- ファイル名に "clean" が含まれる → 正常データ（ラベル0）
- それ以外 → 異常データ（ラベル1）
- すべてのデータを1つのDataFrame（X）とラベル（y）にまとめる

**結果:** 4200件のデータが読み込まれます（正常データ+異常データ）

### セル3: データの確認
```python
X.info()
```

**何をしているか:**
- 読み込んだデータの構造を確認
- 73列の特徴量があることを確認（time列を含む）
- データ型や欠損値の有無をチェック

**重要な発見:** time列はobject型（文字列）なので、機械学習モデルには使えない

### セル4: データの前処理
```python
X = X.drop(columns=['time'])
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)
X_train_sc = sc.fit_transform(X_train)
X_test_sc = sc.transform(X_test)
```

**何をしているか:**
1. **time列の削除**: 機械学習に使えない列を除外（73列 → 72列）
2. **データ分割**:
   - 訓練データ: 70%（2940件）→ モデルを学習させるデータ
   - テストデータ: 30%（1260件）→ モデルの性能を評価するデータ
3. **標準化（StandardScaler）**:
   - 各特徴量の平均を0、標準偏差を1に変換
   - スケールの異なる特徴量（例: CPU使用率0-100% vs システムコール数0-10000）を同じ尺度に揃える
   - これにより機械学習モデルがより正確に学習できる

### セル5: モデルの学習と評価
```python
for model in models:
    clf = model()
    clf.fit(X_train, y_train)
    score = clf.score(X_test, y_test)
```

**何をしているか:**
1. モデルで学習を実施
2. 訓練データでモデルを学習（fit）
3. テストデータで精度を評価（score）

## 仮想環境の終了

作業が終わったら、以下のコマンドで仮想環境を終了できます：

```bash
deactivate
```

## トラブルシューティング

### データが読み込めない場合

- [src/config.py](src/config.py) の `DATA_PATH` が正しいか確認
- データセットのフォルダ構造を確認（CSVファイルがサブフォルダに格納されている必要があります）

### パッケージのインストールでエラーが出る場合

- Pythonのバージョンを確認: `python3 --version`（3.9以上が必要）
- pipを最新版にアップデート: `pip install --upgrade pip`

### Jupyter Notebookが起動しない/コマンドが見つからない場合

**症状:** `jupyter: command not found` と表示される

**解決方法:**
1. 仮想環境が有効化されているか確認（プロンプトに `(venv)` が表示されているか）
2. 仮想環境を有効化してから再度インストール:
   ```bash
   source venv/bin/activate
   pip install jupyter
   ```
3. それでも解決しない場合は、VSCodeで実行する方法（方法A）を試してください

### VSCodeでカーネルが選択できない場合

**解決方法:**
1. VSCodeの拡張機能「Python」と「Jupyter」がインストールされているか確認
2. コマンドパレット（`Cmd + Shift + P`）から「Python: Select Interpreter」を選択
3. `./venv/bin/python3` を選択
4. ノートブックを再度開く

## ファイル構成

```
mldetect/
├── main.ipynb              # メインの実行ノートブック
├── requirements.txt        # 依存パッケージ一覧
├── README.md              # このファイル
└── src/
    ├── __init__.py
    └── config.py          # データセットのパス設定
```

## 注意事項
- 各環境で `requirements.txt` から依存パッケージをインストールしてください

## 機械学習の流れ（まとめ）

1. **データ収集**: IoTデバイスから72種類のメトリクスを収集
2. **前処理**: 不要な列を削除、データを分割、標準化
3. **モデル学習**: 訓練データでモデルを学習
4. **評価**: テストデータで精度を測定
5. **分析**: どの特徴量が重要かを可視化

この流れで、正常なIoTデバイスと異常なデバイスを自動的に見分けられるようになります。
