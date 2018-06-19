# sss
![思维导图]（https://github.com/waallf/object_Detection_summary/new/master）  
![综述网站]（https://handong1587.github.io/deep_learning/2015/10/09/object-detection.html）   
# one-stage  
## SSD  
[论文]（https://arxiv.org/abs/1512.02325）
![ssd 结构图](https://github.com/waallf/object_Detection_summary/new/master/images/SSD.png)
* 特点：  
  1. 继承了yolo中将detection 转化为regression的思路，所以可以一次完成网络训练  
  2. 基于faster-rcnn提出的anchors,提出了相似的prior box  
  3. 加入基于特征金字塔的检测方式（可以利用前面的特征进行分类与回归）  
  
### prior box:  
  就是一些预选框，后面对这些预选框进行softmax与bounding box regression 获得真实的目标位置  
  以feature map上的点为中心，生成一系列的prior-box，然后坐标乘以step，相当于将框映射回原图
## DSSD  
* 在SSD的基础上增加了反卷积层，提高对对小目标检测的效果  
[论文]（https://arxiv.org/abs/1701.06659）  
![DSSD结构图](https://github.com/waallf/object_Detection_summary/new/master/images/DSSD.png)  
* 特点  
  1. 在SSD的基础上增加了反卷积层，融合相邻两层特征图的信息，使得对小物体检测更加准确  
  2. 预测模块增加了跳层链接  
## YOLO  
[论文](http://machinethink.net/blog/yolo-coreml-versus-mps-graph)
* 与Faster-rcnn的不同之处在于，YOLO将目标检测转化为一个回归的问题，而Faster-rcnn先利用RPN网络提取region proposal,然后在对proposal进行回归  
* 工作过程  
（1） 将原图划分为s*s的网格,如果一个目标中心落入某个格子，这个格子就负责检测该目标  
 (2) 每个网格预测b个bounding box,以及c个类别概率  
 （3）每个boundingbox除了要回归自身的位置之外，还要附带预测一个confidence值，这个值 = pr(object)*IOU,如果有目标落在格子里,pr(object)=1,否则=0  
 （4）目标检测的时候，每个的类别条件概率和bounding box预测的confidence信息想乘，就得到每个blunding box的class-specifical confidence score:  
  pr(class) * IOU  
* YOLO中是对坐标进行归一化之后进行的回归  
  w = width(box)/width(image)  
  y = height(box)/width(image)
  x = x/width(grid) - col  
  y = y/geight(grid) - row
## YOLOV2  
## YOLOV3  



# two-stage  
## faster-rcnn  
## R-cnn  
## LIGHT-HEAD RCNN  
## Zoom out-and-In Network with map attention for region proposal and object detection  
[论文](https://arxiv.org/abs/1709.04347)  
![结构图]（https://github.com/waallf/object_Detection_summary/new/master/images/ZOOM1.png）  
![结构图]（https://github.com/waallf/object_Detection_summary/new/master/images/ZOOM2.png）  
* 提出的方法：  
  1. 使用一个卷积网络提取特征，将F3层的特征输入一个atention检测器
  2. attention 检测器由几层卷积组成，输出一个维度为1*1*n的特征图，n为 F1或F2特征图的维度  
  3. 将产生的这个向量与F1，F2想乘即为选出来的特征图  
  4. 还有一个反卷积环节，也可已使用，前面的特征图与对应的反卷积后的特征图相结合后再进行与attention 向量乘  
  5. 将选择后的特征图在进行ROI Pooling ,proposal提取，然后再进行预测，回归  
* 一些训练技巧  
