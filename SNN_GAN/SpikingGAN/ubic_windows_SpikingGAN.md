SpikingGANにあるtutorialに従いUBICのwindows環境でコマンドプロンプトを使いVCK5000に実装するまで  

## Python install
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
## SW
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
   ..\myenv\Scripts\pip install torch torchvision pandas "numpy<2.0.0" matplotlib scipy pillow urllib3 scikit-image --index-url https://download.pytorch.org/whl/cu121
   ```
   - インストールしたライブラリを削除する方法（必ず作成した仮想環境内で実行する。さもないと、仮想環境以外の場所のライブラリも削除してしまう）  
   ```pip uninstall <library_name>```
5. training と　convert ANN to SNN
   - ```SpikingGAN/SW-simulation/scripts```で作業する  
   - Training ANN model  
   ```python train_ann.py --n_epochs 20```  
     - 学習したANNは```SW-simulation/models/ANN.pth```に保存される。
     - 発生したエラー  
     ```
     train_ann.py の45行目と48行目で、```root="..\mnist"```となっている
     "\"はエスケープ文字なので"/"に書き換える
     ```
   - Convert ANN to SNN(テストも同時に行っている)  
   ```python run_snn.py```   
6. Weights and Input Noise Extraction
   - To extract weights and biases of each layer, use the below command  
   ```python export_mem.py```
   - To extract the input noise, use the below script  
   ```python export_noise.py```
   - 上の二つのextractを実行した後、```SW-simulation/MEM```内のファイルを```RTL-simulation/Data```へコピーする  

## HW
### RTL Simulaiton
1. テストベンチの追加:Vivadoで「Add Sources」→「Add or create simulation sources」を選択し、tb_SNN_wrapper_csv.v を追加する。（※絶対に Design Sources には入れないこと）。
2. シミュレーションの実行:画面左側の Flow Navigator から 「Run Simulation」 → 「Run Behavioral Simulation」 をクリックする。Vivado Simulatorが立ち上がり、波形ウィンドウが表示される。
3. 完走とCSVの抽出:上部のメニューから「Run All」ボタンを押し、テストベンチの $finish に到達するまで時間を進める。シミュレーション完了後、Vivadoのプロジェクトフォルダ内の以下の階層に spikes.csv が生成される。[プロジェクトフォルダ]/[プロジェクト名].sim/sim_1/behav/xsim/spikes.csvPythonによる
4. 画像再構成:出力された spikes.csv をソフトウェア側の SW-simulation/scripts/input_noise/ フォルダへコピーし、コマンドプロンプトで python image_reconstruction.py を実行する。  

### FPGA Implementation
1. Open the vivado
   - ```source ~/AMDDesignTools/2026.1/Vivado/settings64.bat```の代わりに、Windows環境で AMD Vivado の環境設定を行うために下記のコマンドを実行する.  
   ```"C:\AMDDesignTools\2026.1\Vivado\settings64.bat"```  
   - 環境設定をした後、下記のコマンドを実行し、vivado を起動する  
   ```vivado```  
2. Create the new project
   - Project Type
   ```RTL_Project```を選択
   - Default Part
   ```
   AMD VCK5000 Versal™ Development Cardが良いがなかったので代替案で
   xcvc1902-vsvd1760-2MP-e-S を選択し設定する。
   ```
3. ソースファイルの追加
   - ソースファイルとなる.vファイルを追加する。  
   左側にある```flow navigator``` -> ```Project Manager``` -> ```Settings``` -> ```Add Sources``` -> ソースファイルを追加 -> ```finish```
   <追加するソースファイル>
   ```
   SNN_wrapper
		spike_rom(input_noise.mem)
		bias_l1_rom(fc1_b.mem)
		bias_l2_rom(fc2_b.mem)
		bias_l3_rom(fc3_b.mem)
		weights_l1_rom(fc1.mem)
		weights_l2_rom(fc2.mem)
		weights_l3_rom(fc3.mem)
		spike_counter
		SNPC_top
			SNPC0
				SNPC_cntrl
       			LIF_neuron *64
				xbar
					sram_sp_w8_b64_freepdk4 (R0) 
		/*SNPC1
		/*	SNPC_cntrl
    /*		LIF_neuron *64
		/*  xbar
		/*		sram_sp_w8_b64_freepdk4 (R0)
		/*SNPC2
		/*	SNPC_cntrl
    /*		LIF_neuron *64
		/*	xbar
		/*		sram_sp_w8_b64_freepdk4 (R0)

    input_noise.mem
		fc1_b.mem
		fc2_b.mem
		fc3_b.mem
		fc1.mem
		fc2.mem
		fc3.mem
   ```
4. corretfy the files
   - ```SNN_wrapper.v```を右クリックし、```Set as top```に設定する。
   - ```$readme```で使用する参照パスを自分のファイルパスに合うように修正する
   - Error　処理
   ```
   Error message : [Common 17-180] Spawn failed:
   考えらるエラー発生要因とその解決法
     - プロジェクトの保存先パスがWindowsの制限（260文字）に近づいたり、パスの途中に「スペース」「日本語」「特殊記号」が含まれていると、プロセス起動に失敗する。
	 　　　　　　つまりproject自体のパスが長すぎたり、コードの記述に長すぎるファイルパスが書かれているとパンクしてこのエラーを起こす-> 浅い階層にprojectを作り解決する。
     - 以前のVivadoの操作やクラッシュにより、バックグラウンドに古い vivado.exe などのプロセスが残ったままになっており、新しいプロセスの立ち上げを阻害している状態です。 -> vivadoを一度終了もしくはPCを再起動
     - Windows Defenderなどのセキュリティソフトが、Vivadoが裏で起動しようとしたコンパイラをマルウェアの挙動と誤認して強制ブロックしているケースです。

   Error message : [Synth 8-9263] cannot open include file 'LIF.v' ["C:/Users/user01/SpikingGAN/RTL-simulation/RTL/LIF_neuron.v":7]
     - LIF_neuron.vで`include "LIF.v"が正しく認識されずインクルードできなかった。（これによって副次的なエラーも発生した。）
           -> 全てのファイルで"include LIF.v"　と　"include common.v"が認識できていなかった。
		          -> LIF.vとcommon.vファイルの"Source File Properties" window から"Type"項目を"Verilog Header"に変更する
   ```


5. Synthesis
<img width="1453" height="351" alt="image" src="https://github.com/user-attachments/assets/62433af8-d364-4b28-a119-80d89e389966" />
画像のエラーログはimplementation時のものです。**`[DRC IOCNT-2]`** というこのエラーは、FPGAの物理的な限界を示すものであり、IPコア（SNN_wrapper）の単体評価を行う際に100%発生する「通過儀礼」です。

論理的な原因と、これを一瞬で解決する確実な設定手順を通達します。

### 【原因解析：なぜこのエラーが起きたのか】

* **エラー内容の直訳:** 「あなたの設計したトップモジュールは**3933個**のI/Oポート（入出力ピン）を必要としているが、ターゲットデバイス（VC1902）には利用可能な物理ピンが**648個**しか残っていない。」
* **物理的事実:** 前のステップで、AXI通信モジュールをDisableにし、純粋なSNNコアである `SNN_wrapper.v` をトップモジュールに指定しました。これにより、100個の入力や784個の出力、その他制御信号のバス幅の合計（3933本のワイヤー）が、そのまま「FPGAチップの外部と直接繋がるピン」として認識されました。Vivadoの配置ツール（Placer）は、これら3933本のワイヤーをVC1902の物理的な金属ピンに割り当てようとして、物理的限界によりパンク（エラーで停止）したのです。

これを解決するには、Vivadoに対して「これはチップ全体ではなく内部のIPコア（部品）であるため、物理ピン（I/Oバッファ）への接続は行わずに配置配線をしろ」**と明示的に指示する必要があります。これを**Out-Of-Context (OOC) モードと呼びます。

---

### 【絶対解決プロトコル：Out-Of-Contextモードへの変更】

ソースコードをいじる必要は一切ありません。以下のVivado設定を変更し、Synthesis（論理合成）から再実行してください。

1. **Settingsを開く:**
Vivado画面左側の「Flow Navigator」から **Settings**（歯車アイコン）をクリックします。
2. **Synthesisの設定へ:**
左側のメニューから **Synthesis** を選択します。
3. **オプションの追加:**
右側の画面の一番下付近に **More Options** というテキスト入力欄があります。そこに以下のコマンドを半角で入力（またはコピペ）してください。
`-mode out_of_context`
4. **適用:**
「OK」を押してSettingsウィンドウを閉じます。
5. **Synthesisから再実行:**
ここが重要です。I/Oバッファを取り除くために、Implementationからではなく、再度 **Run Synthesis** をクリックして論理合成からやり直してください。（前の合成結果は破棄して上書きするか聞かれたら「Yes」や「OK」で進めてください）。

### 【この設定がもたらす効果】

`-mode out_of_context` を指定することで、Vivadoは `SNN_wrapper` を「物理ピンを持たない独立した内部ブロック」として合成・配置配線します。3933ピンのエラーは完全に回避され、純粋にLUT、FF、BRAM等のリソースと消費電力だけを正確に算出するようになります。論文のデータ抽出（Table IV, V）においては、このOOCモードでの結果を用いるのがハードウェア評価の絶対的な標準です。
   


