<div align="center">
<h1> Variational Inference with adversarial learning for end-to-end Singing Voice Conversion based on VITS </h1>
    
[![Hugging Face Spaces](https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Spaces-blue)](https://huggingface.co/spaces/maxmax20160403/sovits5.0)
<img alt="GitHub Repo stars" src="https://img.shields.io/github/stars/PlayVoice/so-vits-svc-5.0">
<img alt="GitHub forks" src="https://img.shields.io/github/forks/PlayVoice/so-vits-svc-5.0">
<img alt="GitHub issues" src="https://img.shields.io/github/issues/PlayVoice/so-vits-svc-5.0">
<img alt="GitHub" src="https://img.shields.io/github/license/PlayVoice/so-vits-svc-5.0">

</div>

- 本项目的目标群体是：深度学习初学者，具备Python和PyTorch的基本操作是使用本项目的前置条件；
- 本项目旨在帮助深度学习初学者，摆脱枯燥的纯理论学习，通过与实践结合，熟练掌握深度学习基本知识；
- 本项目不支持实时变声；（支持需要换掉whisper）
- 本项目不会开发用于其他用途的一键包。（不会指没学会）

![vits-5.0-frame](https://github.com/PlayVoice/so-vits-svc-5.0/assets/16432329/3854b281-8f97-4016-875b-6eb663c92466)

- 【无 泄漏】支持多发音人

- 【捏 音色】创造独有发音人

- 【带 伴奏】也能进行转换，轻度伴奏

- 【用 Excel】进行原始调教，纯手工

https://github.com/PlayVoice/so-vits-svc-5.0/assets/16432329/63858332-cc0d-40e1-a216-6fe8bf638f7c

Powered by [@ShadowVap](https://space.bilibili.com/491283091)

## 模型特点：

| Feature | From | Status | Function |
| :--- | :--- | :--- | :--- |
| whisper | OpenAI | ✅ | 强大的抗噪能力 |
| bigvgan  | NVIDA | ✅ | 抗锯齿与蛇形激活，共振峰更清晰，提升音质明显 |
| natural speech | Microsoft | ✅ | 减少发音错误 |
| neural source-filter | NII | ✅ | 解决断音问题 |
| speaker encoder | Google | ✅ | 音色编码与聚类 |
| GRL for speaker | Ubisoft |✅ | 对抗去音色 |
| SNAC |  Samsung | ✅ | VITS 一句话克隆 |
| SCLN |  Microsoft | ✅ | 改善克隆 |
| PPG perturbation | 本项目 | ✅ | 提升抗噪性和去音色 |
| HuBERT perturbation | 本项目 | ✅ | 提升抗噪性和去音色 |
| VAE perturbation | 本项目 | ✅ | 提升音质 |
| Mix encoder | 本项目 | ✅ | 提升转换稳定性 |
| USP 推理 | 本项目 | ✅ | 提升转换稳定性 |

**USP : 即使unvoice和silence在推理的时候，也有Pitch，这个Pitch平滑链接voice段**
![vits_svc_usp](https://github.com/PlayVoice/so-vits-svc-5.0/assets/16432329/ba733b48-8a89-4612-83e0-a0745587d150)

## 安装环境

1. 安装[PyTorch](https://pytorch.org/get-started/locally/)

2.  安装项目依赖  
    ```
    pip install -i https://pypi.tuna.tsinghua.edu.cn/simple -r requirements.txt
    ```
    **注意：不能额外安装whisper，否则会和代码内置whisper冲突**  

3.  下载[音色编码器](https://drive.google.com/drive/folders/15oeBYf6Qn1edONkVLXe82MzdIi3O_9m3), 把`best_model.pth.tar`放到`speaker_pretrain/`里面 （**不要解压**）

4.  下载[whisper-large-v2模型](https://openaipublic.azureedge.net/main/whisper/models/81f7c96c852ee8fc832187b0132e569d6c3065a3252ed18e56effd0b6a73e524/large-v2.pt)，把`large-v2.pt`放到`whisper_pretrain/`里面

5.  下载[hubert_soft模型](https://github.com/bshall/hubert/releases/tag/v0.1)，把`hubert-soft-0d54a1f4.pt`放到`hubert_pretrain/`里面

6.  下载音高提取模型[crepe full](https://github.com/maxrmorrison/torchcrepe/tree/master/torchcrepe/assets)，把`full.pth`放到`crepe/assets`里面

7.  下载[sovits5.0.pretrain.pth](https://github.com/PlayVoice/so-vits-svc-5.0/releases/tag/5.0/), 把它放到`vits_pretrain/`里面，推理测试

    > python svc_inference.py --config configs/base.yaml --model ./vits_pretrain/sovits5.0.pretrain.pth --spk ./configs/singers/singer0001.npy --wave test.wav

## 数据集准备
1. 人声分离，如果数据集没有BGM直接跳过此步骤（推荐使用[UVR](https://github.com/Anjok07/ultimatevocalremovergui)中的3_HP-Vocal-UVR模型或者htdemucs_ft模型抠出数据集中的人声）  
2. 用[slicer](https://github.com/flutydeer/audio-slicer)剪切音频，whisper要求为小于30秒（建议丢弃不足2秒的音频，短音频大多没有音素，有可能会影响训练效果）  
3. 手动筛选经过第1步和第2步处理过的音频，裁剪或者丢弃杂音明显的音频，如果数据集没有BGM直接跳过此步骤  
4. 用Adobe Audition进行响度平衡处理  
5. 按下面文件结构，将数据集放入dataset_raw目录  
```shell
dataset_raw
├───speaker0
│   ├───000001.wav
│   ├───...
│   └───000xxx.wav
└───speaker1
    ├───000001.wav
    ├───...
    └───000xxx.wav
```

## 数据预处理

```shell
python svc_preprocessing.py -t 2
```
-t：指定线程数，必须是正整数且不得超过CPU总核心数，一般写2就可以了

预处理完成后文件夹结构如下面所示
```shell
data_svc/
└── waves-16k
│    └── speaker0
│    │      ├── 000001.wav
│    │      └── 000xxx.wav
│    └── speaker1
│           ├── 000001.wav
│           └── 000xxx.wav
└── waves-32k
│    └── speaker0
│    │      ├── 000001.wav
│    │      └── 000xxx.wav
│    └── speaker1
│           ├── 000001.wav
│           └── 000xxx.wav
└── pitch
│    └── speaker0
│    │      ├── 000001.pit.npy
│    │      └── 000xxx.pit.npy
│    └── speaker1
│           ├── 000001.pit.npy
│           └── 000xxx.pit.npy
└── hubert
│    └── speaker0
│    │      ├── 000001.vec.npy
│    │      └── 000xxx.vec.npy
│    └── speaker1
│           ├── 000001.vec.npy
│           └── 000xxx.vec.npy
└── whisper
│    └── speaker0
│    │      ├── 000001.ppg.npy
│    │      └── 000xxx.ppg.npy
│    └── speaker1
│           ├── 000001.ppg.npy
│           └── 000xxx.ppg.npy
└── speaker
│    └── speaker0
│    │      ├── 000001.spk.npy
│    │      └── 000xxx.spk.npy
│    └── speaker1
│           ├── 000001.spk.npy
│           └── 000xxx.spk.npy
└── singer
    ├── speaker0.spk.npy
    └── speaker1.spk.npy
```

如果您有编程基础，推荐，逐步完成数据处理，也利于学习内部工作原理

- 1， 重采样

    生成采样率16000Hz音频, 存储路径为：./data_svc/waves-16k

    > python prepare/preprocess_a.py -w ./dataset_raw -o ./data_svc/waves-16k -s 16000

    生成采样率32000Hz音频, 存储路径为：./data_svc/waves-32k

    > python prepare/preprocess_a.py -w ./dataset_raw -o ./data_svc/waves-32k -s 32000

- 2， 使用16K音频，提取音高

    > python prepare/preprocess_crepe.py -w data_svc/waves-16k/ -p data_svc/pitch

- 3， 使用16k音频，提取内容编码
    > python prepare/preprocess_ppg.py -w data_svc/waves-16k/ -p data_svc/whisper

- 4， 使用16k音频，提取内容编码
    > python prepare/preprocess_hubert.py -w data_svc/waves-16k/ -v data_svc/hubert

- 5， 使用16k音频，提取音色编码
    > python prepare/preprocess_speaker.py data_svc/waves-16k/ data_svc/speaker

- 6， 提取音色编码均值；用于推理，也可作为发音人统一音色用于生成训练索引（数据音色变化不大的情况下）
    > python prepare/preprocess_speaker_ave.py data_svc/speaker/ data_svc/singer

- 7， 使用32k音频，提取线性谱
    > python prepare/preprocess_spec.py -w data_svc/waves-32k/ -s data_svc/specs

- 8， 使用32k音频，生成训练索引
    > python prepare/preprocess_train.py

- 9， 训练文件调试
    > python prepare/preprocess_zzz.py

## 训练
0. 参数调整  
  如果基于预训练模型微调，需要下载预训练模型[sovits5.0.pretrain.pth](https://github.com/PlayVoice/so-vits-svc-5.0/releases/tag/5.0)并且放在项目根目录下面  
  并且修改`configs/base.yaml`的参数`pretrain: "./vits_pretrain/sovits5.0.pretrain.pth"`，并适当调小学习率（建议从5e-5开始尝试）  
  `batch_size`：6G显存推荐设置为6，设置为8可以训练，但是一个step的速度会非常慢  

1. 开始训练  
   ```
   python svc_trainer.py -c configs/base.yaml -n sovits5.0
   ```
2. 恢复训练
   ```
   python svc_trainer.py -c configs/base.yaml -n sovits5.0 -p chkpt/sovits5.0/***.pth
   ```
3. 训练日志可视化
   ```
   tensorboard --logdir logs/
   ```

![sovits5 0_base](https://github.com/PlayVoice/so-vits-svc-5.0/assets/16432329/1628e775-5888-4eac-b173-a28dca978faa)

![sovits_spec](https://github.com/PlayVoice/so-vits-svc-5.0/assets/16432329/c4223cf3-b4a0-4325-bec0-6d46d195a1fc)

## 推理
1. 导出推理模型：文本编码器，Flow网络，Decoder网络；判别器和后验编码器等只在训练中使用  
   ```
   python svc_export.py --config configs/base.yaml --checkpoint_path chkpt/sovits5.0/***.pt
   ```
2. 推理  
- 如果不想手动调整f0，只需要最终的推理结果，运行下面的命令即可
  ```
  python svc_inference.py --config configs/base.yaml --model sovits5.0.pth --spk ./data_svc/singer/修改成对应的名称.npy --wave test.wav --shift 0
  ```
- 如果需要手动调整f0，依据下面的流程操作

  - 使用whisper提取内容编码，生成test.ppg.npy
    ```
    python whisper/inference.py -w test.wav -p test.ppg.npy
    ```

  - 使用hubert提取内容编码，生成test.vec.npy
    ```
    python hubert/inference.py -w test.wav -v test.vec.npy
    ```

  - 提取csv文本格式F0参数，用Excel打开csv文件，对照Audition或者SonicVisualiser手动修改错误的F0
     ```
     python pitch/inference.py -w test.wav -p test.csv
     ```
  - 最终推理
     ```
     python svc_inference.py --config configs/base.yaml --model sovits5.0.pth --spk ./data_svc/singer/修改成对应的名称.npy --wave test.wav --ppg test.ppg.npy --vec test.vec.npy --pit test.csv --shift 0
     ```

3. 一些注意点  
    当指定--ppg后，多次推理同一个音频时，可以避免重复提取音频内容编码；没有指定，也会自动提取  
    
    当指定--vec后，多次推理同一个音频时，可以避免重复提取音频内容编码；没有指定，也会自动提取  
    
    当指定--pit后，可以加载手工调教的F0参数；没有指定，也会自动提取  
    
    生成文件在当前目录svc_out.wav
    
    | args | --config | --model | --spk | --wave | --ppg | --vec | --pit | --shift |
    | :---:  | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
    | name | 配置文件 | 模型文件 | 音色文件 | 音频文件 | ppg内容 | hubert内容 | 音高内容 | 升降调 |

## 捏音色
纯属巧合的取名：average -> ave -> eva，夏娃代表者孕育和繁衍
```
python svc_eva.py
```
```python
eva_conf = {
    './configs/singers/singer0022.npy': 0,
    './configs/singers/singer0030.npy': 0,
    './configs/singers/singer0047.npy': 0.5,
    './configs/singers/singer0051.npy': 0.5,
}
```

生成的音色文件为：eva.spk.npy

## 数据集

| Name | URL |
| :--- | :--- |
|KiSing         |http://shijt.site/index.php/2021/05/16/kising-the-first-open-source-mandarin-singing-voice-synthesis-corpus/|
|PopCS          |https://github.com/MoonInTheRiver/DiffSinger/blob/master/resources/apply_form.md|
|opencpop       |https://wenet.org.cn/opencpop/download/|
|Multi-Singer   |https://github.com/Multi-Singer/Multi-Singer.github.io|
|M4Singer       |https://github.com/M4Singer/M4Singer/blob/master/apply_form.md|
|CSD            |https://zenodo.org/record/4785016#.YxqrTbaOMU4|
|KSS            |https://www.kaggle.com/datasets/bryanpark/korean-single-speaker-speech-dataset|
|JVS MuSic      |https://sites.google.com/site/shinnosuketakamichi/research-topics/jvs_music|
|PJS            |https://sites.google.com/site/shinnosuketakamichi/research-topics/pjs_corpus|
|JUST Song      |https://sites.google.com/site/shinnosuketakamichi/publication/jsut-song|
|MUSDB18        |https://sigsep.github.io/datasets/musdb.html#musdb18-compressed-stems|
|DSD100         |https://sigsep.github.io/datasets/dsd100.html|
|Aishell-3      |http://www.aishelltech.com/aishell_3|
|VCTK           |https://datashare.ed.ac.uk/handle/10283/2651|

## 代码来源和参考文献

https://github.com/facebookresearch/speech-resynthesis [paper](https://arxiv.org/abs/2104.00355)

https://github.com/jaywalnut310/vits [paper](https://arxiv.org/abs/2106.06103)

https://github.com/openai/whisper/ [paper](https://arxiv.org/abs/2212.04356)

https://github.com/NVIDIA/BigVGAN [paper](https://arxiv.org/abs/2206.04658)

https://github.com/mindslab-ai/univnet [paper](https://arxiv.org/abs/2106.07889)

https://github.com/nii-yamagishilab/project-NN-Pytorch-scripts/tree/master/project/01-nsf

https://github.com/brentspell/hifi-gan-bwe

https://github.com/mozilla/TTS

https://github.com/bshall/soft-vc

https://github.com/maxrmorrison/torchcrepe

https://github.com/OlaWod/FreeVC [paper](https://arxiv.org/abs/2210.15418)

[SNAC : Speaker-normalized Affine Coupling Layer in Flow-based Architecture for Zero-Shot Multi-Speaker Text-to-Speech](https://github.com/hcy71o/SNAC)

[Adapter-Based Extension of Multi-Speaker Text-to-Speech Model for New Speakers](https://arxiv.org/abs/2211.00585)

[AdaSpeech: Adaptive Text to Speech for Custom Voice](https://arxiv.org/pdf/2103.00993.pdf)

[Cross-Speaker Prosody Transfer on Any Text for Expressive Speech Synthesis](https://github.com/ubisoft/ubisoft-laforge-daft-exprt)

[Learn to Sing by Listening: Building Controllable Virtual Singer by Unsupervised Learning from Voice Recordings](https://arxiv.org/abs/2305.05401)

[Adversarial Speaker Disentanglement Using Unannotated External Data for Self-supervised Representation Based Voice Conversion](https://arxiv.org/pdf/2305.09167.pdf)

[Speaker normalization (GRL) for self-supervised speech emotion recognition](https://arxiv.org/abs/2202.01252)

## 基于数据扰动防止音色泄露的方法

https://github.com/auspicious3000/contentvec/blob/main/contentvec/data/audio/audio_utils_1.py

https://github.com/revsic/torch-nansy/blob/main/utils/augment/praat.py

https://github.com/revsic/torch-nansy/blob/main/utils/augment/peq.py

https://github.com/biggytruck/SpeechSplit2/blob/main/utils.py

https://github.com/OlaWod/FreeVC/blob/main/preprocess_sr.py

## 贡献者

<a href="https://github.com/PlayVoice/so-vits-svc/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=PlayVoice/so-vits-svc" />
</a>

## 交流群
<div align="center">

![炼丹师公会-SVC群聊二维码](https://github.com/PlayVoice/vits_chinese/assets/16432329/1d728f61-be74-4706-9ecf-5cb0be4c094c)

</div>
