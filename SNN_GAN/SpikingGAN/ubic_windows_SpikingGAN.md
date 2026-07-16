SpikingGANにあるtutorialに従いUBICのwindows環境でコマンドプロンプトを使いVCK5000に実装するまで  

### Python install
1. インストーラーをダウンロードする:Python公式サイト (python.org) にアクセスし、黄色の「Download Python 3.x.x」(versionは3.11もしくは3.12)ボタンを押してインストーラーをダウンロードします。  
2. インストーラーを起動し、チェックを入れる（超重要）:これを忘れるとコマンドが認識されません。ダウンロードしたファイルを開きます。最初の画面の下部にある 「Add python.exe to PATH」 （または「Add Python 3.x to PATH」）というチェックボックスに必ずチェックを入れます。  
3. インストールを実行する:チェックを入れたら、一番上の「Install Now」をクリックします。（すでにインストール済みの場合は「Modify」を選び、次の画面で「Add Python to environment variables」にチェックを入れて進めてください）  
4. PCを再起動する:念のため、一度パソコンを再起動して設定を完全に反映させます。
- インストールの完了とversionの確認
  ```python --version```
  コマンド実行後 ```Python <version>``` が表示されれば成功です。

```
pythonの削除方法（使い終わったら自分のコンピュータではないため原則インストールやダウンロードしたものはアンインストールするようにする）  
1.「インストールされているアプリ」を開く:スタートボタンを右クリックし、メニューから「インストールされているアプリ」（または「アプリと機能」）を選択します。
2.Pythonを検索する:アプリ一覧の上部にある検索ボックスに Python と入力します。
3.関連プログラムをアンインストールする:リストに表示された Python 3.x.x と Python Launcher の両方について、右端の「･･･」メニュー（またはアイコン）をクリックし、「アンインストール」を実行します。
4.PCを再起動する:古い環境変数の設定などを完全にクリアするため、一度パソコンを再起動します。  
```
### SW
1. 次のコマンドを```SpikingGAN/SW-simulation/scripts```下で実行する。  
   ```python -m venv ../myenv```  
   - 構築した仮想環境の削除
   ```
   エクスプローラーからGUIで直接作成した仮想環境フォルダ```myenv```を削除する
   もしくは、コマンドプロンプトから```rmdir /s /q ..\myenv``` で削除する
   ```
2. 次に下のコマンドを実行する。(```source ../myenv/bin/activate```の代わり)  
   - ```pip```してインストールするパッケージがシステム全体ではなく myenv フォルダ内に安全に隔離されるように、環境を有効化（アクティベート）する  
   ```..\myenv\Scripts\activate.bat```  
   - これが成功すれば(myenv)がコマンドラインの先頭に出現する。
3. 各種ライブラリをインストールする
   ```
   pip install -r ../requirements.txt
   pip install torch==2.9.1 torchvision==0.24.1
   pip install pandas numpy matplotlib
   ```
   tutorial通りだと上記のコマンドになるがこの環境だと```train_ann.py```実行時に```importError```が発生するので、代わりに下記のコマンドを実行する  
   ```torch```関連のライブラリをインストールするとき```--index-url https://download.pytorch.org/whl/cu121```オプションを付加する  
   (```train_ann.py```ファイル内の記述の```device = torch.device("cuda:0")```は「システムにGPUが存在するかどうか、PyTorchがCUDAに対応しているかどうかを一切確認せず、強制的に1番目のGPU（cuda:0）にデータを転送しろ」という命令なので、CUDAに対応していないPyTorchがこの命令を受け取った時、処理がクラッシュします。なのでそれに対応したPyTorchをインストールするためにこのオプションは必要です。)
   ```
   pip install pandas "numpy<2.0.0" matplotlib scipy pillow urllib3 scikit-image
   pip install torch==2.9.1 torchvision==0.24.1 --index-url https://download.pytorch.org/whl/cu121
   ```
   - インストールしたライブラリを削除する方法（必ず作成した仮想環境内で実行する。さもないと、仮想環境以外の場所のライブラリも削除してしまう）  
   ```pip uninstall <library_name>```
5. training と　convert ANN to SNN
   - ```SpikingGAN/SW-simulation/scripts```で作業する  
   - Training ANN model  
   ```python train_ann.py --n_epochs 20```  
     - 学習したANNは```SW-simulation/models.ANN.pth```に保存される。
     - 発生したエラー  
     ```
     train_ann.py の45行目と48行目で、```root="..\mnist"```となっている
     "\"はエスケープ文字なので"/"に書き換える
     ```
   - Convert ANN to SNN(テストも同時に行っている)  
   ```python run_snn.py```   
6. 

