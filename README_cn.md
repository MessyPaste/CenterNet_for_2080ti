# 让centernet支持2080ti和pytorch 1.x的方法
[原官方的github地址在这里](https://github.com/xingyizhou/CenterNet)  
我已经测试过的环境：
1. Ubuntu 18.04
2. Conda python3.7
3. cuda 10.2
4. 显卡nvidia rtx2080ti
5. 最新的pytorch(1.2.1)

2080ti 无法直接运行 pytorch 0.4.x.，试了docker什么的都不行，实在没办法，但是找到了一篇教程，按照教程弄了下果然可以了，教程有些小问题，我修复了一下，然后下面记录一下了过程，顺便贴上自己的代码[https://github.com/LiuTodd/CenterNet_for_2080ti](https://github.com/LiuTodd/CenterNet_for_2080ti)

## 下面是详细的修改步骤
### 创建一个虚拟的conda环境
注意:先要安装conda

conda create --name CenterNet3.7 --python=3.7   
conda activate CenterNet3.7  
conda install pytorch torchvision -c pytorch  


### 下载官方的代码 CenterNet
git clone https://github.com/xingyizhou/CenterNet  
cd CenterNet/src  
pip install -r requirements.txt  

## 构建NMS
需要到 CenterNet\src\lib\external\setup.py  注释以下内容
```
#extra_compile_args=["-Wno-cpp", "-Wno-unused-function"]
```
cd CenterNet\src\lib\external  
python setup.py install  
python setup.py build_ext --inplace  

## 构建DCN2
需要到 CenterNet\src\lib\models\networks\DCNv2\src\cuda\dcn_v2_cuda.cu  注释一些内容，在下面
**步骤:**  
cd CenterNet\src\lib\models\networks  
rm -rf DCNv2  
git clone https://github.com/CharlesShang/DCNv2  
cd DCNv2  
vim src/cuda/dcn_v2_cuda.cu  
Then uncomment the following code:  
```
	//extern THCState *state;
	THCState *state = at::globalContext().lazyInitCUDA();
```
python setup.py build develop  

## 安装 COCOAPI
这个和官网的没什么差别了  
export COCOAPI=/path/to/clone/cocoapi  
git clone https://github.com/cocodataset/cocoapi.git $COCOAPI  
cd $COCOAPI/PythonAPI  
make  
python setup.py install --user  

## 参考
感谢:  
[xingyizhou/CenterNet/issues/7](https://github.com/xingyizhou/CenterNet/issues/7)  
[CenterNet INSTALL](https://github.com/xingyizhou/CenterNet/blob/master/readme/INSTALL.md)  
