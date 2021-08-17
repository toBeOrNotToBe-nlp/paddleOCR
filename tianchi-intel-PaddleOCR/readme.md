
# 环境配置
安装 paddle 
[开始使用_飞桨-源于产业实践的开源深度学习平台 (paddlepaddle.org.cn)]
(https://www.paddlepaddle.org.cn/install/quick?docurl=/documentation/docs/zh/install/pip/windows-pip.html)

check/check.py检查是否安装成功

# 安装依赖包
***
pip install opencv-python

pip install imgaug

pip install pyclipper

pip install lmdb
***


# 下载权重文件
```
mkdir inference && cd inference/

下载模型
wget https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_server_v2.0_det_infer.tar
wget https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_mobile_v2.0_cls_infer.tar
wget https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_server_v2.0_rec_infer.tar

解压模型
tar -xf ch_ppocr_server_v2.0_rec_infer.tar 
tar -xf ch_ppocr_server_v2.0_det_infer.tar
tar -xf ch_ppocr_mobile_v2.0_cls_infer.tar
```
测试结果
```
python tools/infer/predict_system.py --image_dir="./1.jpg" --det_model_dir="./inference/ch_ppocr_server_v2.0_det_infer/"  --rec_model_dir="./inference/ch_ppocr_server_v2.0_rec_infer/" --cls_model_dir="./inference/ch_ppocr_mobile_v2.0_cls_infer/" --use_angle_cls=True --use_space_char=True

image_dir  测试图片路径，如果想批量预测，改成文件夹即可
det_model_dir  检测文字位置模型权重位置
rec_model_dir  识别文字模型权重位置
cls_model_dir   图片分类模型权重位置（朝 上 下 左 右）
```
识别效果在inference_results里面

# 训练预检测模型
首先下载检测模块的预训练模型：
```
wget https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_server_v2.0_det_train.tar
tar -xf ch_ppocr_server_v2.0_det_train.tar
```
运行
```
python tools/train.py -c configs/det/ch_ppocr_v2.0/ch_det_res18_db_v2.0.yml -o Global.pretrain_weights=./inference/ch_ppocr_server_v2.0_det_train/
-c 配置文件位置
-o 微调权重位置
```
# 对测试集进行预测

训练集的位置等信息在配置文件中修改
train_data/tianchi/下的txt文件是训练数据的标准格式

训练完成后，接下来需要将模型权重导出，用于预测。并对测试集的图片进行预测，写入json。
```
# 将模型导出
python tools/export_model.py -c configs/det/ch_ppocr_v2.0/ch_det_res18_db_v2.0.yml -o Global.pretrained_model=output/ch_db_res18/latest  Global.save_inference_dir=output/ch_db_res18/

Global.pretrained_model 训练保存的模型的位置，等于配置文件当中save_model_dir:的位置 + /latest
Global.save_inference_dir 导出文件位置
```
```
# 对测试集进行预测
python tools/infer/predict_system_tianchi.py --image_dir="./doc/imgs/11.jpg" --det_model_dir="output/ch_db_res18/"  --rec_model_dir="./inference/ch_ppocr_server_v2.0_rec_infer/" --cls_model_dir='./inference/ch_ppocr_mobile_v2.0_cls_infer/' --use_angle_cls=True --use_space_char=True

image_dir 测试图片的位置
det_model_dir 改成训练的模型
```













