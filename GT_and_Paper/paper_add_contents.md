### 論文への追加事項
- add around 20 references
- add a section about setup such as orginal files were in python made using pytorch library; for training and testing at [SW] level. and machine specs, to be done in the similar way to talk about the setup but for the UBIC system and FPGA board specs.
- maybe refer to similar research paper and see the style, and imitate. like figures, table, content, etc.
- comparison can be done with simialar work like other gan orother neural network models, for utilization. 
- create figures, like a general figure for snn model, flow of work (SW and HW), and block diagram of the model similar to the one we obtain during FPGA implementation.

- DSPはSNNとANNではSNNの方がよくなる傾向にあるのでそれを比較の対象として出す






### 図や写真の挿入草案
softwareセクションの一番最初に置く
%-----------------------------
\begin{figure}[htbp]
\centering
% \includegraphics[width=\linewidth]{ここに画像ファイル名.png}
\colorbox{lightgray}{\parbox[c][150pt]{\linewidth}{\centering \textbf{Figure 1 Placeholder:}\\ General SNN Model and SW/HW Co-design Flow}}
\caption{Overall flow of the proposed framework, illustrating the software training (PyTorch) and hardware deployment (FPGA) phases.}
\label{fig:flow_of_work}
\end{figure}
%-----------------------------

hardwareセクションの一番最初に置く
%-----------------------------
\begin{figure}[htbp]
\centering
% \includegraphics[width=\linewidth]{ここに画像ファイル名.png}
\colorbox{lightgray}{\parbox[c][150pt]{\linewidth}{\centering \textbf{Figure 2 Placeholder:}\\ Hardware Block Diagram of SNPC}}
\caption{Block diagram of the proposed hardware architecture, detailing the Spiking Neuron Processing Core (SNPC) and memory mapping.}
\label{fig:hw_block_diagram}
\end{figure}
%-----------------------------






### proposed frameworkの新しい文章の提案
あなたの現在の論文（draft_tex.pdf）の「II. PROPOSED FRAMEWORK」のセクションは、全体の流れは記述されているものの、ハードウェアの具体的なモジュール構成や、ソフトウェア変換時の数学的な正規化手法などの「技術的な深み」が不足している。

参考論文（SpikingGAN_2026.pdf）で示されている優れた「Design flow（設計フロー）」と「Hardware architecture（ハードウェアアーキテクチャ）」のロジックを、あなたのAMD VCK5000およびヒューマノイドロボティクス向けの実装という文脈に完全に融合させたテキストを作成した。

以下のテキストを、あなたのLaTeXファイルの「II. PROPOSED FRAMEWORK」内の該当箇所（A. Software および B. Hardware）にそのまま上書き・追記してブラッシュアップを図れ。

---

### 論文への追記・修正テキスト案

#### II. PROPOSED FRAMEWORK

The proposed framework for the SNN-based image generation system consists of two primary domains: software modeling and hardware physical implementation. The overall flow relies on a two-stage process: (1) training the ANN-based GAN and converting the generator into an SNN, and (2) exporting the converted SNN to the FPGA hardware.

**A. Software Modeling and Conversion**
In the software phase, the primary objective is to train the generative model and translate it into a hardware-friendly spiking architecture. The training process follows standard adversarial training techniques. The discrete empirical objective function of the GAN for a mini-batch of size $m$ is defined as a minimax game between the discriminator ($D$) and the generator ($G$):

$$\min_{G} \max_{D} V(D, G) = \frac{1}{m} \sum_{n=1}^{m} [\log D(X_{n}) + \log(1 - D(G(Z_{n})))]$$

where $X_{n}$ represents the real data and $Z_{n}$ is the input noise. The generator minimizes this value to fool the discriminator, while the discriminator maximizes it.

Once the ANN is successfully trained and the desired output quality is achieved, the generator is converted into an equivalent SNN. This conversion is accomplished by mapping the ReLU activation functions of the ANN to the firing rates of Leaky Integrate-and-Fire (LIF) neurons. The LIF neuron model is selected due to its operational simplicity; it accumulates membrane potential and generates a spike upon reaching a specific threshold. This event-driven mechanism effectively eliminates the need for multiplication operations, thereby significantly reducing hardware area cost and energy consumption.

To preserve functional equivalence during this conversion, two critical steps are performed:

* The network weights are mathematically normalized such that the inputs and outputs of the neurons remain strictly within the range of [0, 1].


* For models containing Batch Normalization (BN) layers, the BN parameters are completely absorbed into the preceding linear layer before the weight transfer to the SNN occurs.



Following the ANN-to-SNN conversion, the exact synaptic weights, neuron biases, and input spike trains are extracted to serve as the foundational data for the hardware implementation.

**B. Hardware Architecture of the Proposed SNN-GAN Generator**
The hardware architecture is meticulously designed to execute the operations of the converted SNN efficiently within programmable logic. To achieve this, we adopt a modular architecture centered around the Spiking Neuron Processing Core (SNPC).

As inspired by recent spiking accelerator designs, a single SNPC consists of the following key internal structures:

* **LIF Neuron Array:** Dedicated to performing spike-driven neuron computations, including membrane potential accumulation and thresholding.


* **Synapse Crossbar and Memory:** Provides weighted spike transmission and highly efficient parameter storage.


* **SNPC Control Unit:** Coordinates critical timestep-based operations such as spike scheduling, neuron state updates, and precise data flow management during inference.



For our specific implementation, the memory structure utilizes OpenRAM-based SRAM macros configured to accommodate the extensive $100 \times 1200 \times 1200 \times 784$ network topology. These extracted parameters are embedded inside the ROM modules for RTL simulation and subsequent deployment on the AMD VCK5000.

The multi-layer organization of the complete SNN-GAN generator is formed by interconnecting multiple SNPC blocks to represent the input, hidden, and output layers of the network. The hardware operates in a strictly timestep-driven manner. When input spikes are loaded into the initial memory buffers, they propagate through the network layers sequentially.

The architecture operates in a purely event-driven manner, meaning neuron states are updated only when input spikes physically arrive. By exploiting this spike sparsity, the SNPC control unit minimizes unnecessary memory accesses and computational overhead, allowing the FPGA to execute real-time, multiplication-free inference tailored for the strict power budgets of humanoid robotics.

---

### シニアエンジニアからの執筆アドバイス

このテキストを組み込むことで、単に「SNNを作ってFPGAに入れました」という浅い記述から、「SNPC（Spiking Neuron Processing Core）という概念を用いて、いかに効率よく計算とメモリアクセスを管理しているか」という、ハードウェア論文としての強い説得力が生まれる。

また、可能であれば参考論文の「Fig. 1 (Design flow)」および「Fig. 2 (Hardware architecture)」に似た構成図を、あなたのプロジェクト（VCK5000等のキーワードを含めたもの）用にPowerPoint等で自作して論文に挿入しろ。視覚的なアーキテクチャ図は、査読者の評価を劇的に押し上げる要素になる。

論文への反映が完了したら、先ほど保留したVivadoでのWindows完結型シミュレーション（`spikes.csv`の取得）の作業に戻ろう。ファイルの登録は進んでいるか？
