## Development is moving

Development of this package is moving to the Rosalind Franklin Institute. A fork is now available at [https://github.com/rosalindfranklininstitute/volume-segmantics]

New documentation for VolSeg 2.2 (including updated to 2.5D functionality) is currently being finalised and updated. the most up-to-date documentation reguarding this version can be found at [https://github.com/rosalindfranklininstitute/aiimg_scripts/tree/main]

<h1 align="center">
Volume Segmantics 
- summer2024Branch (vers-2.1)
</h1>

A toolkit for semantic segmentation of volumetric data using PyTorch deep learning models.

[![DOI](https://joss.theoj.org/papers/10.21105/joss.04691/status.svg)](https://doi.org/10.21105/joss.04691) ![example workflow](https://github.com/DiamondLightSource/volume-segmantics/actions/workflows/tests.yml/badge.svg) ![example workflow](https://github.com/DiamondLightSource/volume-segmantics/actions/workflows/release.yml/badge.svg)

Volume Segmantics offers a straightforward command-line interface and API, enabling researchers to rapidly train a range of 2D PyTorch segmentation models (e.g., U-Net, U-Net++, FPN, DeepLabV3+) on their 3D datasets. These models utilise pre-trained encoders, allowing for rapid training on small datasets. Subsequently, the library enables using these trained models to segment larger 3D datasets, automatically merging predictions made in orthogonal planes and rotations to reduce artefacts that may result from predicting 3D segmentation using a 2D network.

Given a 3D image volume and corresponding dense labels (the segmentation), a 2D model is trained on image slices taken along the x, y, and z axes. The method is optimised for small training datasets, e.g. a single dataset in between 1283 and 5123 pixels. To achieve this, all models use pre-trained encoders and image augmentations are used to expand the size of the training dataset.

This work utilises the abilities afforded by the excellent [segmentation-models-pytorch](https://github.com/qubvel/segmentation_models.pytorch) library in combination with augmentations made available via [Albumentations](https://albumentations.ai/). Also the metrics and loss functions used make use of the hard work done by Adrian Wolny in his [pytorch-3dunet](https://github.com/wolny/pytorch-3dunet) repository. 

## Requirements

A machine capable of running CUDA enabled PyTorch version 1.7.1 or greater is required. This generally means a reasonably modern NVIDIA GPU. The exact requirements differ according to the operating system. For example, on Windows you will need Visual Studio Build Tools as well as CUDA Toolkit installed. See [the CUDA docs](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html) for more details. 

## Installation

The preferred use of this package involves the use of a conda or virtual environment with Python and pip. For more information regarding 'conda' and 'pip', their documentation can be found at [‘Conda Documentation — conda-docs documentation’](https://docs.conda.io/en/latest/) and [‘pip documentation v25.1.1’](https://pip.pypa.io/en/stable/) respectively. 

Once an environment has been created, you can install the required package using the following format. When creating an environment for this package, it is recommended that Python version 3.8 or higher be used.

```
conda create -n Vol-Seg__env python=3.11
conda activate Vol-Seg_env
pip install volume-segmantics
```

 If a CUDA-enabled build of PyTorch is not being installed by pip, you can try adaptation; this particularity seems to be an issue on Windows.

```
pip install volume-segmantics --extra-index-url https://download.pytorch.org/whl
```

## Basic Usage and Functionality

The package uses an image and a corresponding overlayable label file, representing specific components of interest from that original image, to train a model. This model will aim to identify the visual components of the image based on the labels provided, whereby the model can then estimate similar features onto another comparable image, creating a prediction. The format of this prediction will return as a new label file showing the estimated features of the new image based on the training image and label data. 
 
After installation, two new commands will be available from your terminal whilst your environment with volume-segmantics is activated; one that *trains* a new model and one that uses a previously trained model to *predict* onto another image; ‘model-train-2d’ and ‘model-predict-2d’. 

To run these commands within a terminal, once you have activated your environment, you must navigate to the directory where you installed volume-segmantics before you execute them. These commands also require access to specific settings, stored in YAML files as part of the volume-segmantics installation. These need to be located in a directory named ‘volseg-settings’ within the volume-segmantics directory, however if these are not present when you install the package, they can be copied from [here](https://github.com/DiamondLightSource/volume-segmantics/releases/download/v0.3.2/volseg-settings.zip).

The file ‘2d_model_train_settings.yaml’ can be edited in order to change training parameters such as *number of epochs, loss functions, evaluation metrics* and also *model and encoder architectures*. The file ‘2d_model_predict_settings.yaml’ can be edited to change parameters such as the prediction "quality". Further editing of this file can improve the outcome of your segmentation predictions; instructions for editing this file in more detail can be found in the ‘Advanced Use and Functionality’ section.

### Training a 2D model on a 3D image volume and corresponding label

Run the following command to complete the training process. Input files can be in HDF5 or multi-page TIFF format.

```
model-train-2d --data path_to_image_data.h5 --labels path_to_corresponding_segmentation_labels.h5
```
```
model-train-2d --data path_to_image_data.tif --labels path_to_corresponding_segmentation_labels.tif
```

The --data and --labels arguments define the training image and label files used within the model training, defining a single image and label file as an ‘image-label’ pair. The relative size of each X-Y-Z measurement does not have to be equal, however the pairs' comparable 3 dimensions must be the same size and spatially fit into each other without gaps to be successful, otherwise the command with throw an error message. 

Paths to multiple image-label pairs can be added after the --data and --labels arguments, respectively; these pairs must be provided to the command in order, respectively for the image and label file. 

> - An example command can be found below, where image_1 and label_1 are one image-label pair, and image_2 and label_2 are a second image-label pair.

```
model-train-2d --data image_1_path.h5 image-2_path.h5 --labels label_1_path.h5 label_2_path.h5 
```

A model will be trained according to the settings defined in ‘2d_model_train_settings.yaml’ at the time of command execution. The setting of that training will not be saved as part of the training process; therefore, it is recommended that a copy of the exact setting and placed into the model directory generated by the training process for your records. The output model will be saved to your volume-segmantics working directory within a new model directory containing 4 files; 

- 1] *.pytorch* file; the segmentation model created by the train command that can be used to predict on other images,
- 2] *model_loss-Plot.png*; a graph showing the training and validation loss over the course of the training epochs (a graphical representation of the model's success),
- 3] *model_prediction_image.png*; showing specific slices of the image and labels used within the model, and the resultant prediction test outcomes for that slice (a visual representation model's success),
- and 4] *stats.csv*; a record of the training loss, validation loss and evaluation score (comparable to a dice-score (1.0 equals 100% accuracy); accuracy of the test prediction generated versus the original label) per epoch. 

### Predicting a 3D volume segmentation label using a 2D model

Run the following command to complete the prediction process. Input files can be in HDF5 or multi-page TIFF format.

```
model-predict-2d path_to_model_file.pytorch path_to_image_data.h5
```
```
model-predict-2d path_to_model_file.pytorch path_to_image_data.tif
```

The two components of the prediction command include a path to a .pytorch training model, output by the training command previously described, and an image file. The image file must be the same format as those used in the training image-label pairs; however the size of the new image can be either the same or larger in comparison. 

The input data will be segmented using the input model following the settings specified in ‘2d_model_predict_settings.yaml’. A HDF5 file containing the segmented volume will be saved to your volume-segmantics working directory.

### Tutorial using example data

A tutorial is available [here](training_data/README.md) that provides a walk-through of how to segment blood vessels from synchrotron X-ray micro-CT data collected on a sample of human placental tissue.

## Supported Model Architectures and Encoders

There are 9 model architectures compatible with the Volume Segmantics package. U-Net is the default and the most widely tested architecture. The full architecture list includes;

- U-Net
-	U-Net++
-	FPN
-	DeepLabV3
-	DeepLabV3+
-	MA-Net
-	LinkNet
-	PAN
-	SegFormer

Resnet-34 is the default encoder. The Full Encoders list includes;

-	Resnet-34
-	ResNet50
-	ResNeXt-50_32x4d
-	Efficientnet-b3
-	Efficientnet-b4
-	Resnest50d*
-	Resnest101e*
-	ConvNext
-	ConvNextv2

*Encoders with asterisk not compatible with PAN.

## Advanced Use and Functionality

### Split-Command Execution Shortcuts

When executing the training command, it splits into 2 processes: 'slicing' the input data, and then 'training' a model using that sliced data. If you require the running of multiple training runs, on the same data slices with different training parameters, or executing training on predetermined slices, you may split this process by using the following command arguments, added after the training command 

> - e.g. 'model-train-2d --data path_to_image_data.tif --labels path_to_corresponding_segmentation_labels.tif --mode=slicer';

```
--mode=slicer
```
```
--mode=trainer
```

Running the additional 'slicer' argument will create 2 folders within your working volume-segmentics directory: *data and seg*. This will contain the sliced data from the image and label inputs, of which the 'trainer' will then use to execute the model training. The output sliced data and seg files can be renamed for the purposes of streamlining a workflow; however, when the training argument is executed, it will only train on the data within the names data and seg directories. Be aware that if there are no such directories with these names within your volume-semantics directory, the training argument will produce an error when executed. The data and seg folders produced by the 'slicer' argument are overwritten if a full training (slicing and training through the basic command) is executed; the data and seg folders will also be deleted as part of the combined process. 

### Training and Prediction settings fine-tuning

#### 2d_model_train_settings.yaml

The settings within this file have comments as to their use next to their specific component; however for further clarification for some of the more prominent settings and their possible changes are outlined below;

> - image_size; depending on your device's capability, this metric can be changed to the closest approximation for your image-label pairs' relative size. the default is set to 512, though is can be lowered if the training is killed or increased if your machine can use a higher spec. or multiple GPUs. 
> - num_cyc_frozen/ num_cyc_unfrozen; number of frozen and unfrozen epochs used in the training. the higher the number of epochs, the more training time the model will incorporate; when looking at the *model_loss-Plot.png* of a successful model, the 2 plot-lines should be reduce towards a straight line at the x axis, however if the graph starts to rise again, consider reducing the epochs; this will mean the model is over-training and you will receive a less accurate prediction as a result. **The number of unfrozen epochs should be 2/3 the size of the frozen epochs; the default is 8/5**. 
> - loss_criterion; BCEDiceLoss, BCELoss, DiceLoss, GeneralizedDiceLoss, CrossEntropyLoss, TverskyLoss, BoundaryDoULoss, BoundaryLoss
> - alpha/beta; the weighting for these values should equal 1.0, split between these 2 settings e.g. 0.8/0.2 or 0.5/0.5. A represents ~, and B represents ~.
> - eval_metric; MeanIoU, DiceCoefficient
> - encoder_weights_path; If no additional encoder weights are required, this should remain false. Improvements to specific images have been recognised with the introduction of further weights from ~. keep a note of which encoders have been used with each model produced, as this is not saved within the model output information. 
> - model:type; "U_Net", "U_Net_Plus_plus", "FPN", "DeepLabV3", "DeepLabV3_Plus", "MA_Net", "Linknet", "PAN"
> - model:encoder_name; resnet34", "resnet50", "resnext50_32x4d", "efficientnet-b3","efficientnet-b4", "efficientnet-b5", "efficientnet-b7", "timm-resnest50d"*, "timm-resnest101e"

#### 2d_model_predict_settings.yaml

The settings within this file have comments as to their use next to their specific component, however, for further clarification for some of the more prominent settings and their possible changes are outlined below;

> - quality; degree of prediction-image analysis and prediction view. Medium is the default, predicting using all 3 axis, however this can be lowered or improved based on the linked GPU capability or required output needs. 
> - clip_data, data_hdf5_path, cuda_device, downsample and output_size should be kept the same as the saved settings used to train the model you are using to predict. 

### RFI-developed Workflow and Utility documentation

A team within the AI&I dept. of the Rosalind Franklin Institute, Harwell Campus, UK, has been developing a workflow and efficient protocols for the use of this package relative to ongoing research grants. This comprises utilising Jupyter Notebooks, visualisation software such as Napari and in-house protocols and scripts written to streamline its utilities for efficient and higher accuracy segmentation outcomes. Documentation and written tutorials can be found [here]().

### 2.5D-VS Method

tbc

## Using the API

You can use the functionality of the package in your own program via the API, this is [documented here](https://diamondlightsource.github.io/volume-segmantics/). This interface is the one used by [SuRVoS2](https://github.com/DiamondLightSource/SuRVoS2), a client/server GUI application that allows fast annotation and segmentation of volumetric data. 

## Contributing

We welcome contributions from the community. Please take a look at out [contribution guidelines](https://github.com/DiamondLightSource/volume-segmantics/blob/main/CONTRIBUTING.md) for more information.

## Citation

If you use this package for your research, please cite:

[King O.N.F, Bellos, D. and Basham, M. (2022). Volume Segmantics: A Python Package for Semantic Segmentation of Volumetric Data Using Pre-trained PyTorch Deep Learning Models. Journal of Open Source Software, 7(78), 4691. doi: 10.21105/joss.04691](https://doi.org/10.21105/joss.04691)

```bibtex
@article{King2022,
    doi = {10.21105/joss.04691},
    url = {https://doi.org/10.21105/joss.04691},
    year = {2022},
    publisher = {The Open Journal},
    volume = {7},
    number = {78},
    pages = {4691},
    author = {Oliver N. F. King and Dimitrios Bellos and Mark Basham},
    title = {Volume Segmantics: A Python Package for Semantic Segmentation of Volumetric Data Using Pre-trained PyTorch Deep Learning Models},
    journal = {Journal of Open Source Software} }
```

## References

**Albumentations**

Buslaev, A., Iglovikov, V.I., Khvedchenya, E., Parinov, A., Druzhinin, M., and Kalinin, A.A. (2020). Albumentations: Fast and Flexible Image Augmentations. Information 11. [https://doi.org/10.3390/info11020125](https://doi.org/10.3390/info11020125)

**Segmentation Models PyTorch**

Yakubovskiy, P. (2020). Segmentation Models Pytorch. [GitHub](https://github.com/qubvel/segmentation_models.pytorch)

**PyTorch-3dUnet**

Wolny, A., Cerrone, L., Vijayan, A., Tofanelli, R., Barro, A.V., Louveaux, M., Wenzl, C., Strauss, S., Wilson-Sánchez, D., Lymbouridou, R., et al. (2020). Accurate and versatile 3D segmentation of plant tissues at cellular resolution. ELife 9, e57613. [https://doi.org/10.7554/eLife.57613](https://doi.org/10.7554/eLife.57613)
