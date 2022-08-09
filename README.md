arXivTimesにならって、読んだpaperはissueに書いていきます。正確性より量を重視します。
https://github.com/lisosia/cv_knowledge/issues

wiki(メモ置き場)  
https://github.com/lisosia/cv_knowledge/wiki

# Tools
- [timm](https://github.com/rwightman/pytorch-image-models)
- [mmdetection](https://github.com/open-mmlab/mmdetection)
- [segementation_models.pytorch](https://github.com/qubvel/segmentation_models.pytorch)

# Backbone
- Resnet
- Resnext
- SEResnext
- EfficientNet
- [RegNet](https://openaccess.thecvf.com/content_CVPR_2020/papers/Radosavovic_Designing_Network_Design_Spaces_CVPR_2020_paper.pdf)
- [Swin](https://arxiv.org/abs/2103.14030)
- [ConvNeXt](https://arxiv.org/abs/2201.03545)

# Image Classification
### Augmentation
- Cutout
- DropBlock
- Cutmix

# Object Detection

### Two Stage
- faster RCNN  
Two-stageの代表格

- cascade r-cnn  
positive sample するときの IoU 閾値が高いと、位置は正確だが recallは低くなる.
IoUを段階的にrefineしていく branch をつけると良いとこ取りができる（branchは３つでIoU=0.5,0.6,0.7）.

### One Stage
- SSD
- YoloV3

- YoloV4  
paperがサーベイとして有用.  
比較して backbone を CSPDarknet53 に + Attention + Cutmixとかでデータ拡張 + etc.
MSCOCOへの Overfit を感じたが専門じゃないのでよくわらかない.

- [YoloV5](https://github.com/ultralytics/yolov5)  
V4と同じだが, ソースコードがpytorchになっている + pythonでいろいろ便利. ライセンスはGPLなので注意.  
よいまとめ https://www.kaggle.com/c/global-wheat-detection/discussion/172436

- PP-Yolo  
backbornをresnet50-dcnに変更+色々いれる. yolo4より良いと主張.
[qiita記事](https://qiita.com/takoroy/items/275af2b44a68fc4bb356)

- RetinaNet  
focal loss を導入

- CetnerNet (objects as center)  
object center を heatmap として学習.
DLA38やHourglass backbone.

- RepPoints  
マルチFeature版CenterNet + N=9個の点(==grid中央からのoffset)を推論.  
BoxはN個のPointの平均/標準偏差をbox中心/サイズとして推論  
https://qiita.com/takoroy/items/6f4f2a5ffabc20479b66

- FCOS  
CenterNetに centernessを導入.
FPNをくっつけた.
Object sizeごとに、各 Feature Pyramid に振り分けて学習させる (どのサイズをどのFeatureに割り当てるかはハイパラ）
Center9pixelのみをpostiveとしてtrainしたほうが性能が良いという話がある(https://github.com/yqyao/FCOS_PLUS)

- ATSS (Bridging the Gap Between Anchor-based and Anchor-free Detection via Adaptive Training Sample Selection)  
RetinanetとFCOSはFCOSの方が精度が高いとされているが, RetinanetのほうにGroupNormや GIoU loss, Centerness branch などを追加するとほぼ同じ性能になる.
さらに、postive sapmle のとり方を工夫すると、差がなくなる. sampleのとり方を工夫した場合, Anchor数A=1とA=9で性能差がなくなる（工夫する前は差がある）.  
Sampleの仕方：各Feature LevelごとにCenterが近いAnchorをk(=9)個選んだあとに、全体AnchorついてGtとのIoUの平均mと分散sをとりm+s以上のAnchorを選ぶ. こうすることで, １つの Feature Level だけ IoU が大きく異なる場合はそのLevelのAnchorのみをpotiveに、そうでないときは複数LevelのAnchorをpositiveにするといった挙動にできる (=adaptive).

- EfficientDet  
Retinenetベースで. backbone は EfficientNetに変え,入力解像度とFPNのstage数とHeadのstage数をスケールさせた.

# Semantic Segmentation

- U-Net, 2015  
基本となったモデル

- [DeepLabV3](https://arxiv.org/pdf/1802.02611.pdf), 2018  
DeepLabは昔から有名。詳細知らず。

- PSPNet (Pyramid Scene Parsing Network), 2017  
PPM (Pyramid Pooling Module) を導入。  
バックボーンの最終層で、1x1,2x2,3x3,6x6サイズにPooling後、それぞれ 1x1-Convして元のサイズにリサイズして元の層にConcat。  
かなり効いていそう（mIoU +4~）

- [UPerNet](https://arxiv.org/abs/1807.10221v1), ECCV 2018  
"Unified Perceptual Parsing for Scene Understanding" の略  
PSPNetにFPNを加えて、さらにOptoinalな分類ヘッドや第２第３のセグメンテーションヘッドをつけた形。  
SwinやConvNextの論文で、セマンティックセグメンテーションタスクの構造として採用されている（対象タスクはADE20Kデータセット）  

- [Swin Transformer](https://arxiv.org/abs/2103.14030), ICCV 2021  
UPerNet+Swinで実験を行っている。DeepLabV3より高精度（Table 3）。

- [ConvNext](https://arxiv.org/abs/2201.03545), 2022  
UPerNet+Swinで実験を行っている。  
Swinと同程度以上の精度/速度トレードオフを主張（セマンティックセグメンテーションタスクについてはFPS記載はない。Table 4。）
