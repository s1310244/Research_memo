- add around 20 references
- add a section about setup such as orginal files were in python made using pytorch library; for training and testing at [SW] level. and machine specs, to be done in the similar way to talk about the setup but for the UBIC system and FPGA board specs.
- maybe refer to similar research paper and see the style, and imitate. like figures, table, content, etc.
- comparison can be done with simialar work like other gan orother neural network models, for utilization. 
- create figures, like a general figure for snn model, flow of work (SW and HW), and block diagram of the model similar to the one we obtain during FPGA implementation.

- DSPはSNNとANNではSNNの方がよくなる傾向にあるのでそれを比較の対象として出す

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
