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
