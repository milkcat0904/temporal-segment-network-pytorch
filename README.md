# Temporal Segment Networks
tsn model for action recognition on pytorch

1.编译环境 Build
=
	
	build opencv 2.4.13 和 dense_flow 环境

		$ bash build_all.sh
		
	终端输出"All tools built. Happy experimenting!" 表示build成功

2.下载数据集 Download dataset
=
实验数据集采用UCF-101，HMDB51
* [ucf101][UCF101]
* [hmdb51][HMDB51]

3.提取光流 Etract optical flow
=
	$ bash scripts/extract_optical_flow.sh DATASET_PATH OUT_PATH NUMBER_OF_WORKER
	
		DATASET_PATH : 数据集的地址
	
		OUT_PATH ：生成光流图的地址
	
		NUMBER_OF_WORKER ：工作的显卡数量，一般设置为2

一个频频的光流图和frame和放在同一个文件夹下
	
4.提取warped光流图 Etract warped flow
=

* 将`tools/build_of.py`的第89行`--flow_type`的默认值改为`warped_tv11`：

```python
parser.add_argument("--flow_type", type=str, default='warped_tvl1', choices=['tvl1', 'warp_tvl1'])
```

* $ bash scripts/extract_optical_flow.sh DATASET_PATH OUT_PATH NUMBER_OF_WORKER

5.生成标签 Label
=
* ucf101数据集标签：

		$ bash scripts/build_file_list.sh ucf101 FRAME_PATH

* hmdb51数据集标签：

		$ bash scripts/build_file_list.sh hmdb51 FRAME_PATH
	
	FRAME_PATH：光流图（frame）的位置
	
6.训练(Inception-BN) Training
=
* ucf101
	* rgb模型
	
	* flow模型
	
	* rgb-diff模型
	
	* warped flow模型

* hmdb51
	* rgb模型
		
		$ python main.py hmdb51 RGB <hmdb51_rgb_train_list> <hmdb51_rgb_val_list> 
		
		--arch BNInception --num_segments 3 
		
		--gd 20 --lr 0.001 --lr_steps 30 60 --epochs 80 
		
		-b 128 -j 8 --dropout 0.8 
		
		--snapshot_pref ucf101_bninception_ 
		
		--gpus 0 1
		
	* flow模型
	
	* rgb-diff模型
	
	* warped flow模型

[ucf101]:http://crcv.ucf.edu/data/UCF101.php
[hmdb51]:http://serre-lab.clps.brown.edu/resource/hmdb-a-large-human-motion-database/

