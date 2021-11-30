# Fusing ASR frontends in EspNET 2 

## 1. Introduction 

Espnet enables to use a wide range of front-ends for ASR. Front-ends based on spectral feature extraction () and Front-ends with Self-Supervised Learning Representations (SSLR). 
In a recent project we remarked that using a learnable linear combination of FBank front-end and Hubert SSLR front-end led to significant improvements in terms of WER. 
For that reason we provide here a generic way of fusing front-end in espnet toolkit.


## 2. Front-ends and fusion techniques 

### 2.1. Front-ends

Espnet provides two types of frontends : ```default``` (spectral speech features, eg FBanks) and ```s3prl``` (SSLR eg Hubert, wav2vec2). The SSLR front-ends usale in Espnet are inherited from [s3prl project](https://github.com/s3prl/s3prl). It includes for instance TERA, Hubert, wav2vec2, wav2vec 2 xslr, APC, PASE ... 

### 2.2. Fusion techniques

We provide for now a learnable linear fusion technique for combining any number of front-ends from any type. This method showed strong results in previous work. More fusion techniques will be added in the next weeks.


## 3. Practical use : Configuration file (yaml) 

The only file you need to take care to use fusion of front-ends is the yaml configuration file. You need to : 
* specify that you want to use the ```fused``` frontend type (as shown in line ?? in the picture below) 
* list the frontend you want to use under ```frontends``` argument, following the syntax of lines ?? to ?? in the picture bellow. 
* chose a projection dimension (as shown in line ?? in the picture below) 
* chose a fusion method (linear_projection only for now, see line ?? in the picture below)
* DO NOT FORGET to change the pre-encoder input dimension according to the projection dimension you chosed and the number of front-ends you use. The input dimension will be ```NumberOfFrontends * ProjectionDimension```, so in the example provided, we have 3 front-ends and ```proj_dim = 100``` so ```input_dim = 300```.




## 4. Problems and future work

We will study the fusion techniques more in depth in the next week and refine the tools, in case of any question ping me anytime (Dan Berrebbi). 
