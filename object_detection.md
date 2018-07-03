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
对于YOLO的改进  
1. Batch Normalizational:Batch Normalization  
    可以提高模型收敛速度，而且起到一定正则化的效果，降低模型的过拟合。在YOLOV2中，每个卷积层后面都添加了Batch Normalization层，并且不再使用  
    droput  
2.  High Resolution Classifier:  利用Image中高分辨率的图将其进行预训练  

3. Convolutional with Anchor Boexs  
在yolov1中，输入图片最终被划分为7*7网格，每个单元格预测两个边界框，yolov1最后采用的是全连接层直接对边界框进行预测，期中边界框的宽与高是想对整张图像 
大小的，由于各个图片存在不同尺度和长款比的物体，yolov1在训练过程中适应不同物体的形状是比较困难的，这也导致yolov1在精确定位方面比较差  
yolov2借鉴了Faster-Rcnn中RPN网络的anchor box，因为anchorsbox预测的是相对于anchors bbox的offsets值  
yolov2不是采用448*448图片作为输入，而是采用416*416大小，因为yolov2模型下采样总步长是32，对于416*416的图像，最终的特征图大小为13*13，维度  
是奇数，此时特征图的一个中心恰好只有一个中心位置。
yolov1每个cell预测两个box，每个box包含五个值（x,y,w,h,confidence）,confidence包含两个部分（IOU以及包含物体的概率），而yolov2使用anchors boxes之  后每个box都预测一套分类概率值。
4.Fine-Grained Features：
yolov2提出了一种多尺度检测方法，和resnet网络的shortcut类似，将前面更高的特征图输入，这里是通过Passthrouth层抽取前面层每个2*2的局部区域，然后将其   
转化为channel维度，对于26*26(512的特征图，经passthrough层处理之后就变成了13*13*2018的心特征图，然后与后一层的13*13的特征图拼接检测  

5.  Multi-Scale Training：
由于YOLOv2模型中只有卷积层和池化层，所以YOLOv2的输入可以不限于416*416大小的图片。为了增强模型的鲁棒性，YOLOv2采用了多尺度输入训练策略，具体来说就  是在训练过程中每间隔一定的iterations之后改变模型的输入图片大小。由于YOLOv2的下采样总步长为32，输入图片大小选择一系列为32倍数的值：{320,353,…,608}，  输入图片最小为320*320，此时对应的特征图大小为10*10（不是奇数了，确实有点尴尬），而输入图片最大为608*608，对应的特征图大小为19*19。在训练过程，每隔10个iterations随机选择一种输入图片大小，然后只需要修改对最后检测层的处理就可以重新训练。
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
