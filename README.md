# Streaming Transformer
**This repo contains the streaming Transformer of our work ``On the Comparison of Popular End-to-End Models for Large Scale Speech Recognition``, which is based on ESPnet0.6.0. The streaming Transformer includes a streaming encoder, either chunk-based or look-ahead based, and a trigger-attention based decoder.**

We released following models and show reproducible results on Librispeech

*  Streaming_transformer-chunk32 with ESPnet Conv2d Encoder. (https://drive.google.com/file/d/1LSBY_vK50Jxvw_GeiYrPwRtJ0DsKU6zL/view?usp=sharing)

*  Streaming_transformer-chunk32 with VGG Encoder. (https://drive.google.com/file/d/12P6TsxtOCxrHezqgtk0USjSKBsYHIe7K/view?usp=sharing)

*  Streaming_transformer-lookahead with ESPnet Conv2d Encoder. (https://drive.google.com/file/d/1YJQaofzsk9_KsL2W9Zb42sGLRRIKRs9X/view?usp=sharing)

*  Streaming_transformer-lookahead with VGG Encoder. (https://drive.google.com/file/d/1LO_0pPxU5XJffqJMgtx4W4IL-Aih5m0M/view?usp=sharing)

In the future, our plan is to release more Transformer related models for streaming ASR, such as Transformer-Transducer model.


## Results on Librispeech (beam=10)
| Model        | test-clean   |  test-other  |latency  |size  |
| --------   | -----:  | :----:  |:----:  |:----:  |
| streaming_transformer-chunk32-conv2d     | 2.8   |   7.5  | 640ms  | 78M |
| streaming_transformer-chunk32-vgg	| 2.8 | 7.0| 640ms | 78M |
| streaming_transformer-lookahead2-conv2d | 3.0 | 8.6| 1230ms | 78M |
| streaming_transformer-lookahead2-vgg | 2.8 | 7.5 | 1230ms | 78M  |




## Installation
Our installation follow the installation process of ESPnet
### Step 1. setting of the environment
    CUDAROOT=/path/to/cuda
    
    export PATH=$CUDAROOT/bin:$PATH
    export LD_LIBRARY_PATH=$CUDAROOT/lib64:$LD_LIBRARY_PATH
    export CFLAGS="-I$CUDAROOT/include $CFLAGS"
    export CUDA_HOME=$CUDAROOT
    export CUDA_PATH=$CUDAROOT`
### Step 2. installation including Kaldi
    cd tools
    make -j 10
    
## Build a streaming Transformer model
### Step 1. Data Prepare
    cd egs/librispeech/asr1
    ./run.sh 
By default. the processed data will stored in the current directory. You can change the path by editing the scripts.
### Step 2. Viterbi decoding
To train a TA based streaming Transformer, the alignments between CTC paths and transcriptions are required. In our work, we apply Viterbi decoding using the offline Transformer model.

    cd egs/librispeech/asr1
    ./viterbi_decode.sh /path/to/model


### Step 3. Train a streaming Transformer
Here, we train a chunk-based streaming Transformer which is initialized with an offline Transformer provided by ESPnet. Set `enc-init` in `conf/train_streaming_transformer.yaml` to the path of your offline model.

	cd egs/librispeech/asr1
	./train.sh

If you want to train a look-ahead based streaming Transformer, set `chunk` to False and change the `left-window, right-window, dec-left-window, dec-right-window` arguments. The training log is written in `exp/streaming_transformer/train.log`. You can monitor the output through `tail -f exp/streaming_transformer/train.log`

### Step 4. Decoding
Execute the following script with to decoding on test_clean and test_other sets

	./decode.sh num_of_gpu job_per_gpu

### Offline Transformer Reference
Regarding the offline Transformer model, Please visit [here](https://github.com/MarkWuNLP/SemanticMask)



## Results on Librispeech (beam=10)
| Model        | test-clean   |  test-other  |latency  |size  |
| --------   | -----:  | :----:  |:----:  |:----:  |
| Offline Transformer (base model)     | 2.24   |   5.12  | inf  | 78M |
| Offline Transformer (large model)    | 2.08   |   4.95 | inf  | 140M |
