# CenterNet suitable for 2080ti with pytorch 1.x
[Original lab here](https://github.com/xingyizhou/CenterNet)
Already tested environment:
1. Ubuntu 18.04
2. Conda python3.7
3. cuda 10.2
4. nvidia rtx2080ti
5. latest pytorch(1.2.1)

When tring to run code in 2080ti(cuda10.2,pytroch:1.2.1),I found that we can not  run CenterNet in 2080ti directly with pytorch 0.4.x. I found some reference but there are some errors in them.I fix them.You can follow my steps as following or you can use the code directly.

## Steps
### Create a virtual environment using conda
Note: If you do not have conda , you should install conda first.  

conda create --name CenterNet3.7 --python=3.7  
conda activate CenterNet3.7  
conda install pytorch torchvision -c pytorch  

### Clone official CenterNet code
git clone https://github.com/xingyizhou/CenterNet  
cd CenterNet/src  
pip install -r requirements.txt  

## Build NMS
uncomment following code in CenterNet\src\lib\external\setup.py  
```
#extra_compile_args=["-Wno-cpp", "-Wno-unused-function"]
```
cd CenterNet\src\lib\external  
python setup.py install  
python setup.py build_ext --inplace  

## Clone and build original DCN2
uncomment following code in CenterNet\src\lib\models\networks\DCNv2\src\cuda\dcn_v2_cuda.cu  
**Steps:**
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

## Install COCOAPI
export COCOAPI=/path/to/clone/cocoapi  
git clone https://github.com/cocodataset/cocoapi.git $COCOAPI  
cd $COCOAPI/PythonAPI  
make  
python setup.py install --user  

## Reference  
Thanks:  
[xingyizhou/CenterNet/issues/7](https://github.com/xingyizhou/CenterNet/issues/7)  
[CenterNet INSTALL](https://github.com/xingyizhou/CenterNet/blob/master/readme/INSTALL.md)  
