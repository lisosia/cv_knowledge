## 概論

#### ターゲット
- CPU
- Nvidia GPU (ex. Jetson Nano, Jetson TX1)
- TPU
- Intel VPU (and Intel GPU)

#### 最適化の対象
- レイテンシー (Latency)  
入力してから最終出力を得られるまでの時間.  
GPUの場合Batchsize=1で推論->後処理した時間のことをいう.
- スループット (Throughput)  
１秒間に何入力処理できるか.  
Edgeの場合はThroughputよりLatencyのほうが重要なケースが多い (私見).  
GPUの場合, batchsizeを１以上にするとThroughputは上がるが, Latencyは下がる.
- パラメータ数 (Params)  
モデルのパラメータ数.  
速度には直結しない. メモリが本当に限られている場合はパラメタ数の削減が必要になることもある.  
MobilenetV2で使われるDepthwise convは, Paramsは小さくなるが, GPU推論時のLatencyは下がる (cuDNNで最適化されていないため).  

#### 最適化方法
- モデルを小さくする  
タスクによっては, Resnet18とResnext50の性能は変わらない.
- 推論と後処理を並列化する ※Object Detectionなど, 後処理が重い場合
- Qauntization (FP16化, INT8化)  
post-trainingなし/あり どちらもある  
- 枝刈り

#### 最適化方法 ターゲット別 
- GPUの場合  
TensorRTでDeployするのがデファクト.  
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
GPUで早くなるようにNetをDesignしてArchitectureSearchした. 前半はXX-Block(Resnet18,34で使われているもの), 後半は DepthWise, BL(Mobilenet) だと Performance/Latency効率が良い.
NAS全般に言えることだが, Imagenet以外へ適用しても, その効率性は保たれるのだろうか. 保たれないのなら, 少し大きいNetでTrain->枝刈り などが必要になってくるのかもしれない.
- [Once-For-All](https://github.com/mit-han-lab/once-for-all)  
一度のTrainで様々なサイズのモデルを生成できる.  大きなNetからスタートして, progressiveに小さなモデルへShrinkしていく(PS: Progressive Shrink)
[qiita記事](https://qiita.com/takoroy/items/275af2b44a68fc4bb356)
