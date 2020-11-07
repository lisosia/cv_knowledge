## 概論

#### ターゲット
- CPU
- Nvidia GPU (ex. Jetson Nano, Jetson TX1)
- TPU
- Intel VPU (and Intel GPU)

#### 最適化の対象
- Latency  
入力してから最終出力を得られるまでの時間.  
GPUの場合Batchsize=1で推論->後処理した時間のことをいう.
- Throughput  
Edgeの場合はLatencyのほうが重視される傾向にある.  
GPUの場合, batchsizeを１以上にするとThroughputは上がるが, Latencyは下がる.
- パラメータ数 (Parameters)  
速度には直結しない. メモリが本当に限られている場合はパラメタ数の縮小が必要になることもある.  
Depthwise convを使うとParamsは小さくなるが, GPU推論時のLatencyは下がる (cuDNNで最適化されていないため).  
有名なものでは, MobilenetV2, V3 ではdepthwise conv を用いている.

#### 最適化の方法
- Qauntization (FP16化, INT8化)  
post-trainingなし/あり どちらもある
- モデルを小さくする  
タスクによっては, Resnet18とResnext50の性能は変わらない.
- Detectionなど後処理が重い場合, 推論と後処理を並列化する

#### 最適化方法 ターゲット別 
- GPUの場合  
TensorRTでDeployする場合が多い. 
FLOPSやParams と Latency は相関しないので注意.
[GPU-Efficient Networks](https://github.com/idstcv/GPU-Efficient-Networks) のTable8, 9 が良いReference. 

- (Intel) CPUの場合  
FLOPS と Latency がほぼ相関する. CNNだとMobilenetV2 あたりが Good Starting Point.  
DeploymentにOpenVINOを使える.  
FP16化, INT8化でそれぞれ早くなる. AVX512がついている最新のCPUだと上昇幅が大きい. 
参考: [OpenVINOベンチマーク](https://docs.openvinotoolkit.org/latest/openvino_docs_performance_benchmarks.html)

- Intel VPU の場合  
DeploymentにOpenVINOを使える. VPUはFP16に対応(INT8は対応していない)

## Tools

## Reference
- [GPU-Efficient Networks](https://github.com/idstcv/GPU-Efficient-Networks)  
GPUで早くなるようにNetを組んだ
- [Once-For-All](https://github.com/mit-han-lab/once-for-all)  
一度のTrainで様々なサイズのモデルを生成できる.  大きなNetからスタートして, progressiveに小さなモデルへShrinkしていく(PS: Progressive Shrink)
[qiita記事](https://qiita.com/takoroy/items/275af2b44a68fc4bb356)
