# ICPR 文本区域检测

业界现在物体识别领域用的最多的是**proposal region**方法，即通过筛选出候选区域，然后对候选区域进行二分类(object/ no object), 同时还要对对bound box进行回归，这是一般的物体识别所用的方法。
CTPN这个方法是基于 fast rCNN来进行的，具体来说就是同样通过筛选proposal region来识别出文字区域， 不同的是，它修改了RPN方法中的anchor产生window的方式，CTPN方法中的proposal region是固定宽度的(论文中是16px), 这样做的好处是可以发现任意宽度的文字序列。


## CTPN大概流程：
- 先从数据中随机选出两张image，然后输入VGG16网络
- 经过VGG16一系列的conv + relu + pool 之后得到conv5 featrue map，大小 H x W x C
- 接下来是ctpn方法的关键，将一个3 x 3的划窗划过整个feature map,每经过一个位置得到一个3 x 3 x C的特征向量，而对于每个窗口来说，都会有k = 10个anchor，也就是每个anchor产生一个text proposal，依次将这些特征输入一个双向LSTM中
- 双向LSTM后面接一个全连接层，准备输出
- 输出分为三个部分，第一个部分是 2k个vertical coordinate，因为一个anchor用的是中心位置y和矩形框高度表示(宽度固定)，k个text proposal共$2k$个vertical coordinate；2k个score，分别表示text 和 non-text的得分；k个side-refinement，用来精修文本行两端的端点


- 最后使用基于图的文本行构造算法，将得到的一个一个的文本段合并成文本行


#2018.3.15更新

发现了一篇最新的文章，里面的方法可以检测任意方向的文字，准确率较CTPN方法增加了一个数量级，这个方法但是没有开源的实现，论文放在了paper文件夹下，1802.08948，篇名：*Multi-Oriented Scene Text Detection via Corner Localization and Region Segmentation*。

更新了使用CTPN代码完成的数据集的效果图，存放在**/CTPN_results**文件夹下，此结果是在作者训练好的模型上得出的结果。

**Roadmap**

- [ ] CTPN和1802.08948论文完成
- [ ] 完成[CTPN tensorflow实现](https://github.com/eragonruan/text-detection-ctpn)源码的阅读
- [ ] 开始实现1802.08948论文中的方法