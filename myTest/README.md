# Stereo-Matching
This repository mainly contains some basic stereo match algorithm code of basic practice


立体匹配算法主要可分为两大类：基于局部约束和基于全局约束的立体匹配算法．

（一）基于全局约束的立体匹配算法：在本质上属于优化算法，它是将立体匹配问题转化为寻找全局能量函数的最优化问题，其代表算法主要有图割算法、置信度传播算法和协同优化算法等．全局算法能够获得较低的总误匹配率，但算法复杂度较高,很难满足实时的需求，不利于在实际工程中使用．

（二）基于局部约束的立体匹配算法：主要是利用匹配点周围的局部信息进行计算，由于其涉及到的信息量较少，匹配时间较短，因此受到了广泛关注，其代表算法主要有 SAD、SSD、NCC等。

这里的几个算法实现都是局部约束的立体匹配算法.



因为要涉及到 Census Transform 下面简单总结一下 Census Transform



*****************************************
*        Census Transform               *
*****************************************
Census Transform 
在实际场景中,造成亮度差异的原因有很多,如由于左右摄像机不同的视角接受到的光强不一致,摄像机增益、电平可能存在差异,以及图像采集不同通道的噪声不同等，cencus方法保留了窗口中像素的位置特征,对亮度偏差较为鲁棒,简单讲就是能够减少光照差异引起的误匹配。

实现原理：在视图中选取任一点，以该点为中心划出一个例如3 × 3 的矩形，矩形中除中心点之外的每一点都与中心点进行比较，灰度值小于中心点即记为1，灰度大于中心点的则记为0，以所得长度为 8 的只有 0 和 1 的序列,作为该中心点的 census 序列,即中心像素的灰度值被census 序列替换。经过Census Transform 后的图像使用汉明距离计算相似度,所谓图像匹配就是在视差图中找出与参考像素点相似度最高的点，而汉明距正是视差图像素与参考像素相似度的度量。
*****************************************
*             汉明距离                *
*****************************************
具体而言，对于欲求取视差的左右视图，要比较两个视图中两点的相似度，可将此两点的census值逐位进行异或运算，然后计算结果为1 的个数，记为此两点之间的汉明值，汉明值是两点间相似度的一种体现，汉明值愈小，两点相似度愈大实现算法时先异或再统计1的个数即可，汉明距越小即相似度越高。（尽管census变换提高了匹配鲁棒性,但其包含的图像信息有限,原始灰度信息己经完全被抛弃了,因此不能将变换结果用于单像素或较小窗口的匹配,仍需要使用与其他区域匹配方法中类似的匹配窗口）变换过程如图 1 所示。

 

127  126 130                1    1    0

126  128 129      --->    1    *    0         ---> cencus序列 ｛11010101｝

127  131  111               1    0    1                                         

                                                                                               --->  异或 ｛01110010｝ --->汉明距为4

110  126 101              1    0    1

146  120 127      --->  0    *    0          ---> cencus序列  ｛10100111｝

112  101  111             1    1    1
