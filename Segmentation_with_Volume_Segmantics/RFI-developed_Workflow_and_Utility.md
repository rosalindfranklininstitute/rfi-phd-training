<h1 align="center">
'RFI-developed Workflow and Utility - Volume Segmantics (Vol-Seg)'
</h1>

The following summative documentation contains explanations and examples for tried and tested methodologies, functionalities and overall the utility of the Volume-Segmentics PyTorch deep-learning segmentation toolkit in conjunction with other packages. 

The information and instructions below have been compiled from initial and developmental users of the Volume-Segmentics Package within the RFI towards a number of segmentation projects and grants. The purpose behind building this separate documentation was not only for the creation of efficient models with reduced time-frame constraints and implementation of critical workflows, but also to help users with minimal coding experience use the package in conjunction with other software to for maximum utility. 

For an initial overview of the package functionalities, please refer to the *Main Vol-Seg Documentation page*, which can be found [here](https://github.com/rosalindfranklininstitute/aiimg_scripts/).

## RFI-specific Utility and Requirements

Using the Virtual Machines (VMs) on Guacamole, you can utilise its GPU processing power for optimal computation outputs to help with your segmentation work. Accessing Guacamole requires opening the relevant RFI-login portal through a browser; Microsoft Edge is recommended, though it can still be accessed by other browsers such as Google Chrome and Firefox. You can access Ceph or import your work files within Guacamole from another space using applications such as Dropbox or Globus. 

# Initial VM Use, Navigation, and Environment Creation

When opening a Guacamole workstation, you will have access to all of its applications through the menu tab in the bottom-left corner.  Within the VM, you can access the command line using the console terminal tab, and your file browser using the directories tab; you can access the internet using a 'Chromium Web Browser' within the application options of the main menu. You may also edit or customise the workstation using the left-click or cosmetic options within the workstation settings. To access Ceph; open a file browser and use the 'File System' directory within the 'My Computer' section. You may also wish to create folders on the desktop or other user space you can access to conduct your work.

When using the VM space, it is recommended that you create virtual environments to contain the installed packages that you require to complete your work. It is also advisable that you create a folder in an accessible space to store your environments; an example would be */home/'user_space'/envs*. Creating an environment is done through a command line terminal using the following format;

>1 - Load a new terminal session (4.10.3 conda environment); *module load miniconda*
> 
>2 - Create an environment using the 'conda create' command; *conda create -y -p path_to_env/example-env python=py_version*
> - "path_to_env" refers to the full directory path of the environment; /home/'user'/ etc.
> - "example-env" refers to the name of your created environment
> - "py_version" refers to the version of Python used within the environment.
> 
>3 - Activate the environment using the 'conda activate' command; *conda activate path_to_env/example-env*
> - "path_to_env/example-env" refers to the name of your full created environment
> 
>4 - Install your required packages;
> - Installations use either *'pip install'* or *'conda install'*
> 
>5 - Deactivate your environment when you are finished using the 'conda deactivate' command and close the terminal; *conda deactivate path_to_env/example-env*

```
module load miniconda
conda create -y -p path_to_env/example-env python=py_version
conda activate path_to_env/example-env
conda deactivate path_to_env/example-env
```

When conducting your work within the VM, to check the number, memory usage and intensity used by individual processes, you can use the *'htop'* command in a new terminal window, which will display the current VM task output data. You can also display the current usage of your GPU by using the *'nvidia-smi'* command.

A helpful selection of *Python tutorials* can be found at [W-3-Schools](https://www.w3schools.com/python/), and further information relating to *Python documentation* can be found [here](https://docs.python.org/3.11/). Further information relating to *conda* documentation can be found [here](https://anaconda.org/anaconda/conda). Further information relating to *pip* documentation can be found [here](https://pypi.org/project/pip/).

## Vol-Seg Installation

1] When installing Vol-Seg, it is recommended that a new virtual environment be created to house its utility; this is to keep the installed packages and install process separate from other installations that you may want to use in conjunction with your work. Python version 3.11 is recommended for Vol-Seg. A simplified format for this creation is detailed below, using the format as described above.

```
module load miniconda
conda create -y -p path_to_env/Vol_Seg-env python=3.11
conda activate path_to_env/Vol_Seg-env
```

2] After creating an environment, you must create a folder to hold your Vol-Seg directory. This will house all of the relevant packages when you download the repository. This folder should be an easily accessible directory within your individual Ceph space; a recommended location is within a "libs" folder. 

> - You can either make this folder using the file browser, or through the terminal using the 'mkdir' command; e.g. *mkdir libs*.
> - Navigate to the correct storing directory using the *'cd'* command. 

```
cd /ceph/users/'Individual_User'  #Navigate to your user space.
mkdir libs  #Make a 'libs'directory.
cd /ceph/users/'Individual_User'/libs  #Navigate to this created 'libs' directory.
```

~ After navigating to your storage directory, you need to clone the Vol-Seg repository from the RFI-GitHub link using the 'git clone' command below. Once cloned, this will appear as a directory named 'volume-segmantics' containing all of the packages and files needed for the toolkit’s operation; you will then need to navigate into directory to begin using it. 

> - The '--branch' argument refers to the specific update of Vol-Seg you wish to clone and its GitHub link
> - You can check the Vol-Seg repository for recent versions using the *'~~'* command;
> - If there have been bug-fixes or updates, use the *'~~'* to update the Vol-Seg packages to the most recent version list.

```
git clone --branch vs_summer2024 https://github.com/rosalindfranklininstitute/volume-segmantics.git #Cloning the Vol-Seg GitHub repo.
cd /ceph/users/'Individual_User'/libs/volume-segmantics #Navigating to the cloned Vol-Seg repo.

git fetch origin
git checkout vs_summer2024
git pull origin vs_summer2024 
```

3] Once cloned, you can then install the Vol-Seg into your Vol-Seg environment. The installation process uses *'Poetry'* (packaging and dependency management tool) for viewing and collating changes to the toolkit's package library. 

> - You must be within your created Vol-Seg environment and the cloned Vol-Seg directory when you run these commands.
> - You may be required to enter your user password into a pop-up window for the installation of poetry into your user space on the VM. 

```
pip install poetry #Install Poetry 
poetry install  #Use Poetry (one within the Vol-Seg directory) to install the packages. 
```

4] The RFI uses a GPU to run the PyTorch modelling; therefore, you will need to uninstall the default version to upgrade it to a newer version. 

> - The download link can be found using the [get started](https://pytorch.org/get-started/locally/) link; *PyTorch version v2.5.1* and *CUDA 12.1*, within the previous versions [*'previous-versions/'*](https://pytorch.org/get-started/previous-versions/).

```
pip uninstall torch torchvision torchaudio  #Uninstall previous torch dependencies.
pip install torch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu121  #Install new torch dependencies.
```

Opt.] To keep track of your segmentation history, you can optionally install 'Tensorboard'; a package that keeps track of your training and prediction outputs. Further information about using tensorboard can be found [here](https://www.tensorflow.org/tensorboard).

```
pip install tensorboard
```

## Additional Software Packages

### Jupyter

JupyterLab is a well-known development environment for using coding notebooks, and it works hand-in-hand with the workflows and scripts developed within the RFI. Further information about using JupyterLab can be found [here](https://jupyter.org/).

#### Installation

JupyterLab can be installed on your main virtual environment or one that has been newly created. Python version 3.9 or higher is recommended for overall utility, and a simplified format for new environment creation can be found below, using the format described above;

```
module load miniconda
conda create -y -p path_to_env/juptyer-env python=3.11
conda activate path_to_env/jupyter-env
```

To install JupyterLab within your environment, you must first navigate to your user space, where you can then run a 'conda install' command to install it. Once successfully installed, you can then use the command line to open a Jupyter session in an internet browser

> - Navigate to the correct storage directory using the 'cd' command.
> - Use *'jupterlab'* to open a new session. If Jupiter is installed within multiple environments, the session will only be able to access the packages installed within that environment. 

```
cd /ceph/users/'Individual_User'
conda install jupyterlab
jupyterlab
```

#### Basic Functionality

When opening Jupyter for the first time, you can create a new file using the start-up page; it is important that you are able to save any work you do within an easily accessible folder. You can create, delete, view and move your files using the jupyter directory interface; however to open previous notebooks, they must be accessible within the location you have opened the Jupyter session from. Further information relating to Jupyter documentation can be found [here](https://docs.jupyter.org/en/latest/), and a helpful cheat sheet for first-time users can be found [here](https://github.com/iAreful/python-quick-view/blob/main/jupyter-cheat-sheet.md).

### Napari

Napari is a well-known interactive image viewer and editor for biological images and segmentation. It deals with 2D and 3D images and is the default for workflows and scripts developed within the RFI. Further information about using Napari can be found [here]([https://jupyter.org/](https://napari.org/stable/)).

#### Installation

Napari should be installed within a newly created virtual environment; if you wish to use multiple versions of Napari within your session, you will need to make separate environments for each version to stop bugs and errors or overlap in utility. Python version 3.11 or higher is recommended for overall utility. The 2 main versions of Napari that can be used are napari-4 and napari-5; if you are getting consistent bugs with the newer version of Napari, move to the older version (more stable with specific image types and editing protocols). 

> - Napari-5 version 0.5.5 is the most commonly used (fast, increased functionality)
> - Napari-4 version 0.4.19 is also very popular (very stable, more plugins)

A simplified format for new environment creation can be found below, using the format described above; an example for Napari-4 and Napari-5 is also provided.

```
module load miniconda
conda create -y -p path_to_env/napari_4-env python=3.11
conda activate path_to_env/napari_4-env 
```
```
module load miniconda
conda create -y -p path_to_env/napari_5-env python=3.11
conda activate path_to_env/napari_5-env 
```

To install JupyterLab within your environment should not navigate to a specific workspace; Napari works much faster when connected to the main Ceph connection, and saving files requires the designation of a directory through a typical saving window. An example for installing Napari-4 and Napari-5 is also provided.

> - To install napari, *conda forge* is required; *install -c conda-forge*
> - If working with .tiff images, the newer versions of tifffile (image storing and reading Python library) can throw errors when saving, opening large files and plugins. It is recommended that you downgrade the tifffile version to a more stable version *'(2022.8.12)'* compatible with Napari-4 and Napari5 before you start working; *pip uninstall/install/ (tifffile version = 2022.8.12)*
> - To open a session of Napari, use the *'python -m'* command 

```
conda install -c conda-forge napari==0.4.19 pyqt  #Install Napari-4
pip uninstall tifffile  #Uninstall newest version of tifffile
pip install tifffile==2022.8.12  #Install downgraded version of tifffile
python -m napari  #Open new napari session window
```
```
conda install -c conda-forge napari==0.5.5 pyqt  #Install Napari-5
pip uninstall tifffile  #Uninstall newest version of tifffile
pip install tifffile==2022.8.12  #Install downgraded version of tifffile
python -m napari  #Open new napari session window
```

Further information relating to conda forge can be found [here](https://conda-forge.org/docs/).

#### Basic Functionality

When opening napari for the first time, you can import images and previous label tiff layers into the viewer. It is important that you regularly save your work to avoid losing progress. An important plugin needed for segmentation work is the *napari-h5* plugin; it allows you to import *.h5 files* into the viewer and is required for adding and editing prediction labels. To install plugins, go to *plugins>install/Uninstall Plugins>* and use the search bar to find your required packages. Other useful plugins include the *napari-animation* and *napari-chatgpt*; other plugins can be found [here](https://napari-hub.org/). Further information relating to napari documentation can be found [here](https://napari.org/stable/tutorials/start_index.html), and other helpful guides for first time users can be found [here](https://napari.org/stable/howtos/index.html#how-tos).

## Segmentation Workflow Introduction

The Segmentation workflow developed for 3D images includes using smaller crops of image-label pairs from the original image, and prediction onto larger regions of interest (ROI); iterating models onto successively larger ROIS until the full area is segmented. Using smaller initial ROIs reduces the time for manual annotations at the beginning of the process and leads to faster, more efficient segmentations and more often, more accurate outputs. 

### Using Vol-Seg (brief);

When you have successfully installed Vol-Seg into its own environment (see steps above), you can use the following format to enter the correct environment and directory to begin your training and predictions. It is important to note that once you have completed a training model or prediction, you should rename and save them within your individual space away from the volume-segmantics directory; this is to stop you from overwriting your files with newly created ones and keep track of your segmentation history. 

> - The *'model-train-2d'* and *'model-predict-2d'* commands can only be used once you have navigated to the cloned volume-segmantics directory. 
> - Please read the fundamental Vol-Seg documentation information regarding command use and *Vol-Seg Settings* before using the command line. This can be found [here](https://github.com/SamK-RFI/rfi-phd-training/blob/main/Segmentation_with_Volume_Segmantics/README-VolSeg_Updated.md)

```
module load miniconda
conda activate path_to_env/Vol_Seg-env
cd /ceph/users/'Individual_User'/libs/volume-segmantics

model-train-2d --data 'directory_location_image' --labels 'directory_location_labels'
  #'--mode=slicer' for splitting and executing the image slicing
  #'--mode=trainer' for executing training on the predetermined image slices from --mode=slicer
model-predict-2d 'directory_location_segmantics_training__model' 'directory_location_new_image'
```

# Individual Method and Protocol Implementation

Before you start segmentating, you will need to prepare your image; assess the image quality and make sure the file type is correct; tiff is the recommended file format for this workflow. *The better the quality of your image, the better your labels and eventual segmentation prediction will be*. If your image is too large to import into napari, it is suggested that you make a "large-as-possible" crop using the [Cropping a large 3D file image and creating a downsized file script](). Once you have your final 3D image, you can import it into the napari window by dragging and dropping it into the workspace or using File>Open File(s). 

Once imported, you can create a new label layer from scratch using the 'new labels layer' button or import a previous label layer into the window. The Napari tools and options can be found on the right-hand side, and below it is your layer list and additional layer visualisation options. Using the paint-brush/eraser/fill tools, you can create your desired label over your imported image (the unpainted area is treated as the 'background'); below is an example of an image with a label layer (human placenta scan). 

A worked example using a high-definition human-placenta scan (WINSdata) shows the basic utility of some of the functions of this method and can be found [here](). 

Within each layer, you can have multiple sub-layers identifying different biological components; these will display as different colours. To check the number of sub-layers within your parent layer, you can use the *'np.unique'* command within the terminal. 

> - 'Label-layer' refers to the order of the layers; starting at layer 0 and moving upwards (bottom layer would be '0', 3rd from the bottom would be '2'). 

```
import numpy as np
np.unique('Label-layer')
```

If there is ever an issue with saving your work (bug pop-up or error), you can also use the following script in the command terminal of napari to manually save your edited layer using the *'imwrite'* command from tifffile; 

> - 'layer_Designation' is the layer you wish to manually save
> - 'File_Location_with_Name' requires a full path to the directory you wish to save into, and a file name; this should be in the format '/ceph/users/'Individual_User'/.../Filename.tif (.tif is required after your chosen file name).

```
import tifffile
layer = viewer.layers["Layer_Designation"]
tifffile.imwrite('File_Location_with_Name', layer.data, compression=('zlib'), compressionargs={'level': 1})
```

Further information and guidance for using napari can be found in the [viewer tutorial](https://napari.org/stable/tutorials/fundamentals/viewer.html#viewer-tutorial)

## Image cropping, visualisation and editing in Napari

Alongside the tools provided by Napari, there is also a selection of code-based tools for processing your images. *Layer Manipulation, ROI cropping* and *Downsampling* are more utilitarian, however *Erosion/Dilation, Voxel-median Filter* and *Voxel-delete Filter* can decrease your manual annotation time and help produce accurate training labels and final segmentations more efficiently. 

### Utility; Layer Manipulation (labels)

Manipulating label data comes in many forms; *splicing, shifting, combining* and *subtracting* and are the most commonly used small-script inputs you can use on the command line to augment your layers to suit your purposes. 

~ A *splice* involves creating a new label layer containing a chosen sub-layer from a parent layer; if you have 2 sub-layer labels in one parent layer, a splice will take your chosen sub-layer from that parent layer, copy it, and create a new layer with only that sub-layer data within it. You may choose both which sub-layer the newly created layer is copied into, alongside which sub-layer from the parent layer is chosen to be spliced. The code for a simple splice can be found below and can be copied straight into and amended in the Napari terminal.

> - 'OLayer' refers to the parent layer chosen.
> - 'SLayer' refers to the sub-layer within the parent layer chosen
> - 'SLD' (refers to the 'Sub-Layer Designation' of the newly created layer from the splice.

```
lA = viewer.layers['OLayer'].data  #'OLayer'; index (starts as [0]) is the parent layer in napari (e.g. prediction model 1).
lA_specific = (lA == 'SLayer') * 1   #'SLayer'; index (starts as [0]) is layer within the parent layer (e.g. 1=tissue).
lX = ((lA_specific) > 0) * 'SLD'  #'SLD'; the SLayer' designation of the newly created copy layer (e.g. 2=cell1).
viewer.add_labels(lX)  #Creation the new layer.
```

The default for a spliced sub-layer should be layer 1 (where 0 is the background, or anything not painted), however the spliced data can instead be *shifted* into another sub-layer designation using the 'SLD' (Sub-Layer Designation). 

> e.g. *lA_specific = (lA == 'SLayer') * 1 -> lX = ((lA_specific) > 0) * 2* would splice the data from lA and copy it "shifted" into sub-layer 2 within the newly created label.

~ A *simple combination* involved the addition of 2 separate layers (both with a single sub-layer within them), forming a newly created layer; You may choose both layers to be combined. When executed, if the result produces additional/overlapping layers where the two combined layers were to mix; use the *complex combination* method and keep the sub-layer designations the same. The code for a simple combination can be found below and can be copied straight into and amended in the Napari terminal.

> - Both sub-layer designations within each parent layer must be the same; otherwise, the 2 layers will overlap and not merge. 

```
l1 = viewer.layers['Layer1'].data  #'Layer1'; index (starts as [0]) is the parent layer in napari (e.g. prediction model 1.1).
l2 = viewer.layers['Layer2'].data  #'Layer2'; index (starts as [0]) is the parent layer in napari (e.g. prediction model 1.2).
lcombined = ((l1 + l2))  #Combination of data.
viewer.add_labels(lcombined)  #Creation the new layer.
```

~ A *complex combination* involves designating both the parent layer and sub-layer of the labels you wish to combine. You can choose both which parent layer, and the sub-layer within them you with to combine. With this code, you can also decide the sub-layer designation of the label data. The code for a complex combination can be found below and can be copied straight into and amended in the Napari terminal.

```
l1 = viewer.layers['Layer1'].data  #'Layer1'; index (starts as [0]) is the parent layer in napari (e.g. prediction model 1.1)
l2 = viewer.layers['Layer2'].data  #'Layer2'; index (starts as [0]) is the parent layer in napari (e.g. previous labels 1)
l1_specific = (l1 == 'sub_Layer1') * 1   #'sub_Layer1'; index (starts as [0]) is layer within the parent layer (e.g. 2=cells1)
l2_specific = (l2 == 'sub_Layer2') * 1   #'sub_Layer2'; index (starts as [0]) is layer within the parent layer (e.g. 1=previous label)
lcombined = ((l1_specific + l2_specific) > 0) * 'SLD' #Combination of data where 'SLD'; the SLayer' designation of the newly created copy layer (e.g. 2=cell1).
viewer.add_labels(lcombined) #Creation of the new layer.
```

~ *Simple and complex combination* can be turned into *subtraction* alternatives by swapping the addition sign within the 'Combination of data' line to a subtraction. It should be noted that when you subtract, the second parent layer designated (l2) will be the data subtracted from the first layer designated (l1). 

> l2 will be subtracted from l1 *'(l1_specific - l2_specific) > 0'*

### Protocol; ROI cropping (image and label)

In napari, when you input an image, the default position is the Z-axis (you can view the data through the other axis using the axis shuffle button); if you are unsure about which axis you are currently viewing, refer to the number shown to the left side of the slice scrollbar (0=Z, 1=Y, 2=X). Taking an ROI crop of a larger 3D image or label layer requires you to know the 3D coordinates of the required cube in 3D space before you run the short-script. The co-ordinates needed are delineated by 6 numbers; the start (s) and end (e) point for each axis you require to form the necessary volume. 

> The 6-point co-ordinates are given by the formula; "range_z", "range_y", "range_x", where the range has a start number and end number split between a colon ':' (Zs:Ze, Ys:Ye, Xs:Xe)
> - This volume can be any dimension; however the script will throw an error if the numbers are outside of the image/labels designated space.
> - e.g. for a 700-cube within a 2000 original image/label; 20:720, 130:730, 1200:1900

The co-ordinates can be derived by using the Napari-builtins plug-in (already installed into your napari package at installation); dragging your mouse cursor over an image (not a label layer; without an image, the labels will have no anchor and therefore give incorrect co-ordinates) will display the cursors current position in 3D space as a 6-point reference at the bottom of the napari, window within a square bracket. The recommended starting point for deciding these coordinates is viewing the data from slice 0 on the Z-axis; a guide for finalising your ROI coordinate numbers can be found below.

>1 - Inspect your main image in all 3 axis and choose the area of the image/label you wish to crop; be mindful of the size of the image you wish to create (if you have a set size in mind, make a rough note of the dimensions in the form of "range_z", "range_y", "range_x").
>
>2 - Navigate to the Z-axis and scroll to the earliest slice (left-most) on the current plane view, which you wish to use (Zs); this is the starting Z-axis co-ordinate.
> 
>3 - Use your cursor, and visualise a square around your chosen crop area; place your cursor in the top-left-hand corner of this square and observe the numbers displayed in the napari builtins display.
> - Your cursor placement will show your current slice location (in the Z-axis position, which should not change when you move your cursor), alongside the position of the starting Y and X axis positions in the following format: (Zs, Ys, Xs).
> - *The Y coordinate will start from the top of the image and increase as you move down an image layer, whereas the X coordinate will start from the left and increase as you move right across the image*.
> - Make a note of these 3 numbers and input them into your 6-point coordinate reference.
>   
>4 - Scroll right along the Z-axis using the slice scroller until you view the last slice on the current plane view, which you wish to use (Ze); this is the ending Z-axis co-ordinate.
>   
>5 - Use your cursor, and again visualise the square around your chosen crop area; place your cursor in the bottom right-hand corner of this square and observe the numbers displayed in the napari builtins display.
> - Your cursor placement will show your current slice location (in the Z-axis position, which should not change when you move your cursor), alongside the position of the ending Y and X axis positions in the following format; (Ze, Ye, Xe).
> - *The Y coordinate will start from the top of the image and increase as you move down an image layer, whereas the X coordinate will start from the left and increase as you move right across the image*.
> - Make a note of these 3 numbers and input them into your 6-point co-ordinate reference.
>   
>6 - **Note**; when forming the 6-point co-ordinate reference, the numbers should form 3 pairs, the difference in each pair will be the range of the length, width and breadth of your volume, and should match any previous ideas of set size. The size of the crop can be checked using the '.shape' command within the cropping short-script. 

Once you have your co-ordinates, you can place them into the code formula for images and label layers respectively; it is important to note that for training, a crop from an image and label pair must be the same size, and must superimpose over each other. *Use the same co-ordinated for both the image and label layer.* The short-script will output a new layer containing your crop, without disturbing your original image and its size.  The code for ROI cropping can be found below and can be copied straight into and amended in the Napari terminal. 

> - 'Layer' is the image or parent label layer you want to be cropped. 

```
#Image cropping
img = viewer.layers['Layer'].data #'Layer'; index (starts as [0]) is the parent layer in napari (e.g. prediction model 1.1).
img.shape  #View the shape of the original data volume

img_roi = img["range_z", "range_y", "range_x"]  #Designate crop shape and size; must be in the format [#:#, #:#, #:#]
img_roi.shape  #View and check the shape of the ROI data volume to be cropped

viewer.add_image(img_roi)  #Creation the new layer.
```

```
#Label-Layer cropping
l = viewer.layers['Layer'].data  #'Layer'; index (starts as [0]) is the parent layer in napari (e.g. prediction model 1.1).
l.shape  #View the shape of the original data volume

l_roi = l["range_z", "range_y", "range_x"]  #Designate crop shape and size; must be in the format [#:#, #:#, #:#]
l_roi.shape  #View and check the shape of the ROI data volume to be cropped

viewer.add_labels(l_roi)  #Creation the new layer.
```

### Protocol: Downsampling (image and label)

If you have an image that is large in both size and data capacity, Napari may sometimes struggle with opening it. If this is the case, and in other cases where this protocol is needed, you may want to compress the image and/or labels without reducing their clarity. Downsampling involves taking information away from the image, reducing the data size, without compromising its quality. The code for a downsampling can be found below and can be copied straight into and amended in the Napari terminal.

> - Examples for downsampling by 2 and by 4 are given, where 2 is half of the original image's size and 4 is a quarter of the original image's size. It is recommended that you use as much data as possible.

```
#Downsampling by a Factor of 2
DSimage = viewer.layers['layer'].data  #Asign data 
DSfinal = DSimage[::2,::2,::2]  #Downsample data
viewer.add_labels(DSfinal)  #Create new layer 
```
```
#Downsampling by a Factor of 4
DSimage = viewer.layers['layer'].data  #Asign data 
DSfinal = DSimage[::4,::4,::4]  #Downsample data
viewer.add_labels(DSfinal)  #Create new layer 
```

### Protocol; Erosion and Dilation (labels)

Erosion and dilation are 2 useful tools for editing label layers; they edit the boundaries of a label, either expanding or decreasing its size by a specific factor. The factor is measured in 3D voxels, which can be specified and changed depending on the size of the erosion or dilation required. The voxels require a 3-digit size designation called a structure_element, where an image mask is used to calculate the area in the background to be filled or deleted relative to its 3D shape. 

This short-script uses the binary_erosion and binary_dilation functions of scipy-ndimage. The code for an erosion and dilation can be found below and can be copied straight into and amended in the Napari terminal.

> - 'Layer' is the label layer you want to be eroded or dilated. 
> - (x, x, x) is the size of the voxel space chosen to erode or dilate by; 3 is a typical size displayed as (3, 3, 3)

```
#Erosion script
from scipy import ndimage
binary_mask = viewer.layers['Layer'].data  #'Layer'; index (starts as [0]) is the parent layer in napari (e.g. prediction model 1.1).
binary_mask = binary_mask.astype(np.uint8)  #Set data to binary
structuring_element = np.ones(('x, x, x'))  #Set structuring_element parameter; where (x, x, x) is the size of the voxel space in 3D
eroded_mask = ndimage.binary_erosion(binary_mask, structure=structuring_element)  #Calculate erosion mask
viewer.add_labels(eroded_mask)  #Create new layer 
```
```
~Dilation script
from scipy import ndimage
binary_mask = viewer.layers['Layer'].data  #'Layer'; index (starts as [0]) is the parent layer in napari (e.g. prediction model 1.1).
binary_mask = binary_mask.astype(np.uint8)  #Set data to binary
structuring_element = np.ones(('x, x, x'))  #Set structuring_element parameter; where (x, x, x) is the size of the voxel space in 3D
dilated_mask = ndimage.binary_dilation(binary_mask, structure=structuring_element)  #Calculate erosion mask
viewer.add_labels(dilated_mask)  #Create new layer 
```

### Protocol; Voxel-Median Filter (labels)

Predictions can often produce noisy or unwanted sharp boundaries in labels; deleting these unwanted small imperfections on the boundaries can be done using a smoothing-filter. The Voxel-Median Filter acts as a smoothing code for rounding off sharp boundaries and finding the best-fitting and consistent edge to a label layer. It uses a filtering size to define an averaged approximation of the label boundary between slices in 3D space along all 3 axes, and outputs the median overlapping option. 

The short-script uses scipy-ndimage and creates a median_filter function, where you can then choose the degree of smoothing and designate the layer you want to edit. It is recommended that you find the best smoothing_size by running iterations of the filter; the smoothing can impact the accuracy of your segmentation layer and reduce the detail you have previously put in. A typical smoothing_size is **3** though can be higher, and it is suggested that you do not go above a smoothing_size of 9, as it tends tom create straight lines and overfit the median average. The code for the smoothing voxel-median filter can be found below and can be copied straight into and amended in the Napari terminal.

> - 'Layer' is the label layer you want to be affected. 
> - The smoothing size can be input from '2' onwards (2-9), with the increasing number size equating to a larger degree of smoothing.
> - The *'def median_filter'* command creating the filter takes the binary image data ('binary_image.astype(bool)') and returns a mask of the data that has been smoothed relative to the 'filter_size'.

```
import numpy as np
from scipy import ndimage
from scipy import io
def median_filter(binary_image, filter_size='smoothing_size'):  #'smoothing_size'; the voxel-filter size or smoothing degree required
    binary_image = binary_image.astype(bool)
    filtered_image = ndimage.median_filter(binary_image, size=filter_size)
    return filtered_image * 1
seg = viewer.layers['Layer'].data  #'Layer'; index (starts as [0]) is the parent layer in napari (e.g. prediction model 1.1).
median_image = median_filter(seg) #Calculate median filter
viewer.add_labels(median_image) #Create new layer 
```

### Protocol; Voxel-Component_Delete Filter (labels)

Prediction images can contain small false positives within the data; shown as specs of label-layer material within the segmentation. This is likely due to noise, and even the best performing models will contain them. Segmentations will also often contain smaller unwanted artefacts that are separated from the main label components, and can be time-consuming to erase manually. The Voxel-Component_Delete Filter deletes the unconnected smaller components of a label layer, allowing for more consistent and polished post-processed label-layers. It searches for objects of a chosen min_size and creates a mask that removes them from the label layer.

The short-script uses scipy-ndimage and creates a filter_components function, where you can then define a minimum size of object to delete; this minimum size is represented as a single integer, the cubic volume of a 3d space (an object fully surrounded by background that fits into the cubic volume will be highlighted by the function as an unwanted component). A cubic volume of 5-5-5 (w/h/l) will be represented as a voxel_size of 125 (5x5x5). 

The size of the voxel can range from 2 onwards, though Napari will likely be killed above 30^3-voxels. If you run a command deleting many small components with a high-voxel component-delete filter, you may run the risk of killing the window; it is recommended that you run progressively larger component deletions to avoid this from happening (saving between each iteration), checking for unwanted component deletions as you progress. The code for the smoothing voxel-component delete filter can be found below and can be copied straight into and amended in the Napari terminal.

> - 'Layer' is the label layer you want to be affected. 
> - The voxel size can be input as a single integer, with the increasing number size equating to a larger deletion factor; 3=9, 5=125, 9=729, 14=2744.
> - The *'def filter_componenets'* command creates the filter that defines the data volume and the minimum size of objects wanted for deletion.

```
import numpy as np
from scipy.ndimage import label, sum
def filter_components(volume, min_size):
    labeled_volume, num_features = label(volume)
    sizes = sum(volume, labeled_volume, index=range(1, num_features + 1))
    mask = np.isin(labeled_volume, np.where(sizes >= min_size)[0] + 1)
    filtered_volume = mask * volume
    return filtered_volume
seg = viewer.layers['Layer'].data  #'Layer'; index (starts as [0]) is the parent layer in napari (e.g. prediction model 1.1).
del_comp = filter_components(seg, 'Voxel-size')  #Create deletion filter with a specific 'Voxel_size'; the cubic minimum size or deletion 5=125, 9=729 etc.
viewer.add_labels(del_comp)  #Create new layer 
```

## Image Processing scripts

More complex scripts that have been developed for further utilities that can not be used directly within napari; requiring many components and packages, they can instead be used in Jupyter Lab, where images and labels can be imported as data, manipulated and then output to be opened again in napari. Master versions of these notebook scripts have been uploaded to this repository and can be copied and opened within your own Jupyter session and then executed at your discretion. It is recomended that you keep these copied notebooks in an easily accessible place and that you fill out as much information within the initial cells to keep track of what is being run and has already been completed. 

*Crop_Downsize* and *Manual_Dicescore* are more utilitarian, however *Voxel-Fill_Holes and *Flood-Fill_Hole* can decrease your manual annotation time and help produce accurate training labels and final segmentations more efficiently.

### Script; Crop_Downsize: Cropping a large 3D file image and creating a downsized file

This script allows you to take a large .h5 file (usually obtained directly from a microscopy session or otherwise) and view the data in a Jupyter notebook if the data file is too large to be opened in napari. The notebook then allows you to crop the original data file into manageable ROIs and also output a downsized .tif file of the data. The script has a full explanation of the code, written for non-coders, and duplicatable cells that use its functionality as intended.

The Jupyter file can be found in the 'Jupyter_notebooks' folder linked to this documentation [here](https://github.com/rosalindfranklininstitute/aiimg_scripts/blob/sam-branch/Jupyter_Notebooks/Crop_Downsize_MASTER.ipynb).

### Script; Manual_Dicescore: Calculation of Prediction accuracy vs a GroundTruth.

This script allows you to calculate the DiceScore of a prediction segmentation with respect to a second 'groundtruth' segmentation. The groundtruth should be a labels layer that is as near perfect to the required output as possible, whereby the DiceScore measures the accuracy of a prediction over 100% (represented as a 1.00 decimal). The notebook assigns 2 .tif files to label data and then uses tensors within the MONAI package to calculate the shapes of both files relative to each other; it then prints a DiceScore as described in the notebook cells of which can be copied. The script has a full explanation of the code, written for non-coders, and duplicatable cells that use its functionality as intended.

The Jupyter file can be found in the 'Jupyter_notebooks' folder linked to this documentation [here](https://github.com/rosalindfranklininstitute/aiimg_scripts/blob/sam-branch/Jupyter_Notebooks/Manual_DiceScore_MASTER.ipynb).

### Script; Voxel-Fill_Holes Protocol (2D and 3D).

This script allows you to run a 'Fill-Holes' protocol, infilling holes within a label layer of a specific size of lower in both 3D and 2D. The 2 methods have slightly different ways of finding and calculating the hole size that is to be infilled; the most utilitarian of the 2 methods is the 2D version. The notebook outputs a .tif file of the labels layer with the holes of a specific size filled. The script has a full explanation of the code, written for non-coders, and duplicatable cells that use its functionality as intended.

The Jupyter file can be found in the 'Jupyter_notebooks' folder linked to this documentation [here](https://github.com/rosalindfranklininstitute/aiimg_scripts/blob/sam-branch/Jupyter_Notebooks/Voxel-Fill_Holes_MASTER.ipynb).

### Script; Flood-Fill_Holes Protocol 

TBC
