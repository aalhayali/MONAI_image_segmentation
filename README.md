# MONAI image segmentation

In this repo, I am investigating the utility of [MONAI](https://monai.io/). MONAI's goal is to "to accelerate the pace of innovation and clinical translation by building a robust software framework that benefits nearly every level of medical imaging, deep learning research, and deployment". Essentially, less code for rapid prototyping. For reference on how hairy code can get, refer to my repo [here](https://github.com/aalhayali/MR-loc_segmentation). It shows how data handling (which is usually the biggest hurdle) can consume up to 50-60% of the code when compared to a Pytorch optimisation loop. 

For the purposes of showing code succinctness, I will performing 3D UNet based image segmentation using the MR-localiser images I used for my previous segmentation project.

# Dependencies

1. `MONAI`
2. `nibabel`
3. `torch`

# Loading and training data

Data in this repo was loaded in a {image,label} dictionary format. I personally find this as a nifty <sub>not the file format</sub> feature to combine data and associated label (analogous to `zip` function in Python). 

# Current issue

**NOTE** - An issue was opened [here](https://github.com/Project-MONAI/MONAI/discussions/5372)

The data I loaded has the dimension **(512,512,7)**, and when the data is loaded in prior to the training and testing pipelines, it's transformed to **(2,1,7,512,512)** == **(batch size, channel, slice, H, W)**. The channel size is casuing a discrepancy when passed through model training, specifically at _line 25_ in the optimisation loop cell. Even when tinkering with the channel numbers a little, the model will not learn, as shown here:
![image](https://user-images.githubusercontent.com/65701637/200875400-1f611938-4f0d-4a56-9df7-c1964b860edc.png)

This can be fixed by a custom optimisation loop, however, the goal of this work is to keep it exclusively to MONAI. 

I ran a segmentation task using the [BRATS](http://braintumorsegmentation.org/), and was successful in generating decent masks. This can be also shown here in [this tutorial](https://github.com/Project-MONAI/tutorials/blob/main/3d_segmentation/brats_segmentation_3d.ipynb). I do believe this library is promising, as loading the data was seamless and the error was a shape mismatch which can be fixed with sometime staring at this problem.

**Verdict** - In terms of code succinctness, MONAI utilised 570 lines of code from imports to test data analysis versus 1100 when writing code from scratch. Shape errors need to be addressed though, and better documentation is needed.
