# Flower Power
## Background
This repository is designed for building a pipeline for a start point of NWO project.


## Datasets and model
The dataset was taken across ten weeks in 2021 in NL, by XXX.
The tool is Sony alpha 7. The height is around 1.5m when taking the picture.

In December 2021, we labeled the first nine weeks dataset using [LabelImg](https://github.com/tzutalin/labelImg).
We labeled two species: madeliefje and paardenbloem.

After labeling, we decided to use [YOLOv5](https://github.com/ultralytics/yolov5).
## Usecase
There are three use cases: 

- Locally train with a prelabeled dataset, which were taken in 2021 in NL;
- Visualize madliefje or paadenbloem in one picture.
- Count the sum of madeliefje and paardenbloem in one folder

The last two usecases rely on the results of the first one. 

## Guidelines
### Sync locally

```
git clone --recursive https://dagshub.com/xiaohenghuang/flowerpower.git
```
Or 
```
git clone https://dagshub.com/xiaohenghuang/flowerpower.git
cd flowerpower
git submodule init
git submodule update
```

### Check your CUDA version (if you want to use GPU, recommended)
Because this repository was developed with CUDA 10.1. Thus, you need to change the *requirements.txt* file in *flowerpower* folder.
 
- Check the CUDA version: in your terminal, perform ```nvidia-smi```.

- Go to [this website](https://pytorch.org/get-started/previous-versions/), if the CUDA version is difference than 10.1.

- Revise the corresponding versions for the following packges and link in *requirements.txt* file:
    - torch==1.8.1+cu101
    - torchvision==0.9.1+cu101
    - torchaudio==0.8.1
    - --find-link https://download.pytorch.org/whl/torch_stable.html

### Create a working environment

- Create an empty environment. If you are using Anaconda, after create the empty environment, please first ```conda install pip```. 
- Perform ```pip install -r requirements.txt```

### Download our labeled dataset
- Perform ```cd flowerpower```
- Perform ```dvc pull -r origin```. After perform this command, you may have errors about two missing folders and two missing files:
 
>...
>
>A       test_data\
A       labeled_data\
2 files added and 4 files failed
ERROR: failed to pull data from the cloud - Checkout failed for following targets:
D:\test\flowerpower\yolov5\runs\detect\try
D:\test\flowerpower\autosplit_train.txt
D:\test\flowerpower\autosplit_val.txt
D:\test\flowerpower\yolov5\runs\train\test\weights\best.pt
Is your cache up to date?
<https://error.dvc.org/missing-files> 

However, this is normal since we have not run the piepeline yet.

### Use cases
We designed three use cases depends on how comfortable you are with coding.
However, you need to perform the first usecase anyway to have a trained customized model. 
#### 1. Train locally
Perform ```dvc repro``` to run through the pipeline. 
The input for this pipeline is in *labeled_data* folder (for traning and validating the model) and 
*test_data* folder (the test dataset). By default, the *labeled_data* is the first nine weeks labeled data mentioned in **Datasets and model** section.
The *test_data* is the tenth week photos.

The output of this pipeline is in *flowerpower\yolov5\runs\detect\try* folder. You will find:

- The labeled photos in *test_data*

- Their crosponding YOLO formats in *flowerpower\yolov5\runs\detect\try\labels* folder.

Nevertheless, you may have the following error:

> RuntimeError: Unable to find a valid cuDNN algorithm to run convolution
ERROR: failed to reproduce 'dvc.yaml': failed to run: python yolov5/train.py --img 2112 --rect --batch 2 --epochs 60 --data ./data.yaml --weights yolov5s.pt --name test --exist-ok, exited
with 1

This is caused by GPU limitation. If you have this error, you need to open *dvc.yaml* file.
Try shrink the following parameters in ```cmd``` in ```train``` stage:

- ```--img 2112``` maybe to ```--img 640```
- ```--batch 2``` maybe to ```--batch 1```

Sadly, model quality will suffer from changes of ```--img```. **Most importantly**, if you change the ```--img 2112```
in ```cmd``` in ```train``` stage, please have the same ```--img``` for ```inference``` stage.

#### 2. (Webapp) Visualize and count madeliefje and paardenbloem in one picture

- Perform the use case 1 steps to have *best.pt* in folder *flowerpower\yolov5\runs\train\test\weights\* .
- Put *flowerpower\yolov5\runs\train\test\weights\best.pt* in *best_model* folder. 
- Perform ```streamlit run src/app.py```

#### 3. (Webapp) Flow through one folder

- Perform the use case 1 steps to have *best.pt* in folder *flowerpower\yolov5\runs\train\test\weights\* .
- Put *flowerpower\yolov5\runs\train\test\weights\best.pt* in *best_model* folder. 
- Perform ```streamlit run src/folderflowapp.py```




