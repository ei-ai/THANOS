
# HAT

## Contents
* [수정한 부분](#수정한-부분)  
* [실행 및 세팅](#실행-및-세팅)  
  * [Data Preparation](#data-preparation)  
  * [Training](#training)  
  * [Testing](#testing)  



## 수정한 부분
[errors](https://github.com/ei-ai/HAT/tree/main/errors) 참고고

## 실행 및 세팅
### Dependencies
<details>
<summary> MacOS </summary>
<div markdown=1>

* OS: Sonoma 14.1
* GPU: 14개(Apple M3 Pro)
* Python = 3.9.21
* requirements.txt
    ```sh
    build==1.2.2.post1
    cffi==1.17.1
    click==8.1.8
    colorama==0.4.6
    coloredlogs==15.0.1
    ConfigArgParse==1.7
    Cython==3.0.11
    -e git+https://github.com/mit-han-lab/hardware-aware-transformers.git@70e5a279d080670208249fdd98ed731fa9bcc466#egg=fairseq
    fastBPE @ file:///Users/(생략)/hardware-aware-transformers/fastBPE
    filelock==3.16.1
    flatbuffers==24.12.23
    fsspec==2024.12.0
    humanfriendly==10.0
    importlib_metadata==8.5.0
    Jinja2==3.1.5
    joblib==1.4.2
    lxml==5.3.0
    MarkupSafe==3.0.2
    mpmath==1.3.0
    networkx==3.2.1
    numpy==2.0.2
    onnx==1.17.0
    onnxruntime==1.19.2
    packaging==24.2
    portalocker==3.0.0
    protobuf==5.29.2
    pycparser==2.22
    pyproject_hooks==1.2.0
    regex==2024.11.6
    sacrebleu==2.4.3
    sacremoses==0.1.1
    sympy==1.13.1
    tabulate==0.9.0
    tensorboardX==2.6.2.2
    tomli==2.2.1
    torch==2.5.1
    tqdm==4.67.1
    typing_extensions==4.12.2
    ujson==5.10.0
    zipp==3.21.0
    ```

</div>
</details>

<details>
<summary> Ububtu </summary>
<div markdown=1>

* OS: Ubuntu 22.04 LTS (Windows WSL 사용)
* Python = 3.9.21
* requirements.txt
    ```sh
    cffi==1.17.1
    click==8.1.8
    colorama==0.4.6
    coloredlogs==15.0.1
    ConfigArgParse==1.7
    Cython==3.0.11
    -e git+https://github.com/mit-han-lab/hardware-aware-transformers.git@70e5a279d080670208249fdd98ed731fa9bcc466#egg=fairseq
    fastBPE==0.1.1
    filelock==3.16.1
    flatbuffers==24.12.23
    fsspec==2024.12.0
    humanfriendly==10.0
    Jinja2==3.1.5
    joblib==1.4.2
    lxml==5.3.0
    MarkupSafe==3.0.2
    mpmath==1.3.0
    networkx==3.2.1
    numpy==2.0.2
    nvidia-cublas-cu12==12.4.5.8
    nvidia-cuda-cupti-cu12==12.4.127
    nvidia-cuda-nvrtc-cu12==12.4.127
    nvidia-cuda-runtime-cu12==12.4.127
    nvidia-cudnn-cu12==9.1.0.70
    nvidia-cufft-cu12==11.2.1.3
    nvidia-curand-cu12==10.3.5.147
    nvidia-cusolver-cu12==11.6.1.9
    nvidia-cusparse-cu12==12.3.1.170
    nvidia-nccl-cu12==2.21.5
    nvidia-nvjitlink-cu12==12.4.127
    nvidia-nvtx-cu12==12.4.127
    onnx==1.17.0
    onnxruntime==1.19.2
    packaging==24.2
    portalocker==3.1.1
    protobuf==5.29.2
    pycparser==2.22
    regex==2024.11.6
    sacrebleu==2.5.0
    sacremoses==0.1.1
    sympy==1.13.1
    tabulate==0.9.0
    tensorboardX==2.6.2.2
    torch==2.5.1
    tqdm==4.67.1
    triton==3.1.0
    typing_extensions==4.12.2
    ujson==5.10.0
    ```

</div>
</details>



### Data Preparation
```sh
bash configs/[task_name]/get_preprocessed.sh
```
```sh
bash configs/wmt14.en-de/get_preprocessed.sh
bash configs/wmt14.en-fr/get_preprocessed.sh
bash configs/wmt19.en-de/get_preprocessed.sh
bash configs/iwslt14.de-en/get_preprocessed.sh
```


### Training
1. Train a supertransformer
    * train
    ```sh
    python train.py --configs=configs/wmt14.en-de/supertransformer/space0.yml
    python train.py --configs=configs/wmt14.en-fr/supertransformer/space0.yml
    python train.py --configs=configs/wmt19.en-de/supertransformer/space0.yml
    python train.py --configs=configs/iwslt14.de-en/supertransformer/space1.yml
    ```
    * download
    ```sh
    python download_model.py --model-name=[model_name]
    ```
    ```sh
    python download_model.py --model-name=HAT_wmt14ende_super_space0
    python download_model.py --model-name=HAT_wmt14enfr_super_space0
    python download_model.py --model-name=HAT_wmt19ende_super_space0
    python download_model.py --model-name=HAT_iwslt14deen_super_space1
    ```
    * convert(수정중)    
    convert supertransformer   
    onnx  
    ```sh
    python convert_onnx.py --dataset-name=[dataset_name] --configs=configs/[task_name]/convert_onnx/[search_space].yml
    ```
    ```sh
    python convert_onnx.py --dataset-name=wmt14ende --configs=configs/wmt14.en-de/convert_onnx/space0.yml
    python convert_onnx.py --dataset-name=wmt14enfr --configs=configs/wmt14.en-fr/convert_onnx/space0.yml
    python convert_onnx.py --dataset-name=wmt19ende --configs=configs/wmt19.en-de/convert_onnx/space0.yml
    python convert_onnx.py --dataset-name=iwslt14deen --configs=configs/iwslt14.de-en/convert_onnx/space1.yml
    ```
    rknn  
    ```sh
    python convert_rknn.py --dataset-name=[dataset_name]
    ```
    ```sh
    python convert_rknn.py --dataset-name=wmt14ende
    python convert_rknn.py --dataset-name=wmt14enfr
    python convert_rknn.py --dataset-name=wmt19ende
    python convert_rknn.py --dataset-name=iwslt14deen
    ```

2. Evolutionary Search  
    2.1 Generate a latency dataset
    ```sh
    python latency_dataset.py --configs=configs/[task_name]/latency_dataset/[hardware_name].yml
    ```
    ```sh
    python latency_dataset.py --configs=configs/iwslt14.de-en/latency_dataset/gpu_titanxp.yml
    python latency_dataset.py --configs=configs/wmt14.en-de/latency_dataset/cpu_raspberrypi.yml
    python latency_dataset.py --configs=configs/wmt14.en-de/latency_dataset/cpu_xeon.yml
    python latency_dataset.py --configs=configs/wmt14.en-de/latency_dataset/gpu_titanxp.yml
    python latency_dataset.py --configs=configs/wmt14.en-fr/latency_dataset/cpu_raspberrypi.yml
    python latency_dataset.py --configs=configs/wmt14.en-fr/latency_dataset/cpu_xeon.yml
    python latency_dataset.py --configs=configs/wmt14.en-fr/latency_dataset/gpu_titanxp.yml
    python latency_dataset.py --configs=configs/wmt19.en-de/latency_dataset/gpu_titanxp.yml
    ```
    2.2 Train a latency predictor
    ```sh
    python latency_predictor.py --configs=configs/[task_name]/latency_predictor/[hardware_name].yml
    ```
    ```sh
    python latency_predictor.py --configs=configs/iwslt14.de-en/latency_predictor/gpu_titanxp.yml
    python latency_predictor.py --configs=configs/wmt14.en-de/latency_predictor/cpu_raspberrypi.yml
    python latency_predictor.py --configs=configs/wmt14.en-de/latency_predictor/cpu_xeon.yml
    python latency_predictor.py --configs=configs/wmt14.en-de/latency_predictor/gpu_titanxp.yml
    python latency_predictor.py --configs=configs/wmt14.en-fr/latency_predictor/cpu_raspberrypi.yml
    python latency_predictor.py --configs=configs/wmt14.en-fr/latency_predictor/cpu_xeon.yml
    python latency_predictor.py --configs=configs/wmt14.en-fr/latency_predictor/gpu_titanxp.yml
    python latency_predictor.py --configs=configs/wmt19.en-de/latency_predictor/gpu_titanxp.yml
    ```
    2.3 Run evolutionary search with a latency constraint  

4. Train a Searched SubTransformer



### Testing

