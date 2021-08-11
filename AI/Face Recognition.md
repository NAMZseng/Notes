# Face Recognition

## 1 定位

- 将图像转换成黑白

- 将黑白图像处理成体现图像的明暗变化的HOG图像（Histogram of Oriented Gradients），与已知的人脸HOG 图案对比，定位人脸区域

  <img src="https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/v2-7930174bf2222944549ff6bf14660aae_720w.jpg" alt="img" style="zoom:67%;" />

## 2 矫正

对于电脑来说，面朝不同方向的同一张脸，是不同的，所以需要将图片矫正，使得脸部的特征点总在图片中的样本位置（sample place）

- 找到**68** 个人脸上普遍存在的特定点（称为**特征点**， landmarks）——包括下巴的顶部、每只眼睛的外部轮廓、每条眉毛的内部轮廓等

- 将图像进行仿射变换，使得特征点尽可能靠近中心

  <img src="https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/v2-34f4018d500a7a12d7f0624a149c647d_720w.jpg" alt="img" style="zoom: 50%;" />

## 3 编码

通过预训练的深度学习[网络](https://github.com/cmusatyalab/openface/tree/master/models/openface)来处理矫正的脸部图像，获得 **128** 个测量值

## 4 比对

将未知的人脸图像与已标识的人脸图像的128个测试值进行比对，计算其欧几里得距离，当小于预设值（0.6）时，就认为俩个图像是同一个人。

```python
obama_face_encoding = face_recognition.face_encodings(obama_image)[0]
unknown_face_encoding = face_recognition.face_encodings(unknown_image)[0]
    
known_faces = [
    obama_face_encoding
]
    
results = face_recognition.compare_faces(known_faces, unknown_face_encoding)
```

## 参考文章

- https://github.com/ageitgey/face_recognition/blob/master/README_Simplified_Chinese.md
- https://zhuanlan.zhihu.com/p/24567586
- http://lear.inrialpes.fr/people/triggs/pubs/Dalal-cvpr05.pdf
- https://www.csc.kth.se/~vahidk/papers/KazemiCVPR14.pdf

