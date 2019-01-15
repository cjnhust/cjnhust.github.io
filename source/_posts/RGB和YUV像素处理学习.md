---
title: RGB和YUV格式及处理学习
tag: 音视频相关
---

总结一下最近网上看到的RGB和YUV相关资料

### RGB的几种格式

* 5:5:5
* 5:6:5
* 8:8:8
* 8:8:8:8

### YUV的几种格式

* 4:4:4  数据长度为24位，每一组y都对应一组u和一组v
* 4:2:0 数据长度为12位，每四个y对应一对uv
* 4:2:2 数据长度为16位 每两个y对应一对uv

数据的存储分为planar和packed

packed是YUV交替存储在数据中

planar是Y,U,V分别存储互不干涉，即现存储所有的Y，接着存储所有的U，最后存储所有的V

#####  (1)YUYV(YUV422)

![](/images/YUYV.png)

YUYV为YUV422采样的存储格式中的一种，存储格式为packed模式,相邻的两个Y共用其相邻的两个Cb、Cr，分析，对于像素点Y'00、Y'01 而言，其Cb、Cr的值均为 Cb00、Cr00，其他的像素点的YUV取值依次类推。

##### (2)UYVY(YUV422)

![img](/images/UYVY.png)

UYVY格式也是YUV422采样的存储格式中的一种，存储格式为packed模式只不过与YUYV不同的是UV的排列顺序不一样而已，还原其每个像素点的YUV值的方法与上面一样。

##### (3)YUV422P(**属于YUV422**)

YUV422P也属于YUV422的一种，它是Planar模式，如上图所示。其每一个像素点的YUV值提取方法也是遵循YUV422格式的最基本提取方法，即两个Y共用一个UV。比如，对于像素点Y'00、Y'01 而言，其Cb、Cr的值均为 Cb00、Cr00。

##### (4)YV12,YU12(**属于YUV420**)

YU12和YV12属于YUV420格式，也是一种Planar模式，将Y、U、V分量分别打包，依次存储。其每一个像素点的YUV数据提取遵循YUV420格式的提取方式，即4个Y分量共用一组UV。注意，上图中，Y'00、Y'01、Y'10、Y'11共用Cr00、Cb00，其他依次类推。

![Figure 12. YV12 memory layout ](/images/YV12.gif)

分离这种格式YUV分量的c代码如下

```c
int yuv420_split(char *url, int w, int h, int num) {
    FILE *fp_source = fopen(url, "rb+");
    FILE *fp_y = fopen("/out/yuv420_y.yuv", "wb+");
    FILE *fp_u = fopen("/out/yuv420_u.yuv", "wb+");
    FILE *fp_v = fopen("/out/yuv420_v.yuv", "wb+");
    unsigned char *pic = (unsigned char *) malloc(w * h * 3 / 2 * c_l);
    for (int i = 0; i < num; ++i) {
        fread(pic, c_l, w * h * 3 / 2 * c_l, fp_source);
        fwrite(pic, c_l, w * h * c_l, fp_y);
        fwrite(pic + w * h, c_l, w * h / 4 * c_l, fp_u);
        fwrite(pic + w * h * 5 / 4, c_l, w * h / 4 * c_l, fp_v);
    }
    free(pic);
    fclose(fp_source);
    fclose(fp_y);
    fclose(fp_u);
    fclose(fp_v);
    return 0;

}
```

##### (5)NV12、NV21(**属于YUV420**)

NV12和NV21属于YUV420格式，是一种two-plane模式，即Y和UV分为两个Plane，但是UV（CbCr）为交错存储，而不是分为三个plane。其提取方式与上一种类似，即Y'00、Y'01、Y'10、Y'11共用Cr00、Cb00，NV12和NV21的区别在于前者UV交错存储时U在前面，后者V在前面，NV12结构如下图所示，NV21类似只是UV顺序相反

![Figure 13. NV12 memory layout ](/images/NV12.gif)

YUV RGB 相互转化



**PS:查看ffmpeg所有支持格式的命令**

```shell
./ffmpeg -pix_fmts
```



### 参考资料

https://blog.csdn.net/leixiaohua1020/article/details/50534150

http://blog.51cto.com/ticktick/555791