# CS 7180 Advanced Perception -ColorConstancy
Final Project: Color Constancy
OS : MacOs
IDE : Google Colab

Submitted by: Venkateshwaran Sundar (sundar.ve@northeastern.edu)

Github: https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/tree/ColorConstancy_Final


Computational Color constancy for linear images

I've attempted to achieve state-of-the-art results for  computational color constancy on linear using the MobileNet model.

State-of-the-art angular error : Mean: 1.672695, Median: 0.986252, TriMean: 1.456510, Best25: 0.329702, Worst25: 4.238528
Cube+ dataset:

<img width="874" alt="Screenshot 2023-10-13 at 8 34 30 PM" src="https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/assets/106343437/723ba4be-8720-461b-9cc4-584aa8de8603">

Reference: https://ipg.fer.hr/ipg/resources/color_constancy

I've used 600 images from this dataset to train an Imagenet pre-trained MobileNetv2 model. I used this model to understand how well color constancy can be computed using a lightweight model.
I tested this model on 200 images from the Cube+ dataset.

Setup:
To begin with, I realized that an image from this dataset has a green tinge, and will need some preprocessing before viewing it.
PNG image from the dataset:
![image](https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/assets/106343437/bdd2b8ca-bc20-40b5-b404-7f532de1d8e4)


This is what the image looks like, after linearizing the values, and applying the ground truth illumination:
![image](https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/assets/106343437/fd94f6f5-0171-4216-9143-a758e99edced)

I then proceeded to preprocess images to work as inputs for MobilNetV2. These are the things i did:
 
 *Resize images to size (224,224)
 
 *Normalise image to have values from [-1,1]

#MobileNetV2 architecture
<img width="999" alt="image" src="https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/assets/106343437/c0362d86-b81b-44de-8c10-6b696937e467">

One salient feature(in my opinion) for this model is that my loss function is "Angular error" (as opposed to MSE).
My intuition is that, since the metric for color constancy performance is an angular error, it would be useful to set the loss function for optimization to be the same.

So, I trained the model and performed predictions of 200 test images from the Cube+ dataset, and this is what I got for Angular error:

Mean: 5.207108231765006<br/>
Median: 1.061212293545509<br/>
Trimean: 2.01843737570492<br/>
Average of the Lowest 25%: 0.30633756652674365<br/>
Average of the Highest 25%: 17.28117165474485<br/>

In comparison, when the loss function was Mean Squared error, this was the metrics for Angular error:<br/>
Mean: 6.937295205977416<br/>
Median: 4.2797557829048225<br/>
Trimean: 4.967636278881123<br/>
Average of the Lowest 25%: 2.696353296734367<br/>
The average of the Highest 25%: 15.730027304618213<br/>

Since Trimean is a good measure of central tendency, looks like using Angular error as a loss function has paid some dividends, with Trimean reducing by 2 points.
All metrics are better with this loss function in the model.

Here's another example of ground truth compared to my prediction.

Ground truth(had to resize image, to fit this file):
![GT](https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/assets/106343437/533d8cd2-5ecf-4de6-8ff5-2bf92a73cb2b)
Here's the image corrected using prediction illumination values:
![prediction](https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/assets/106343437/186beeab-28b0-4d12-abf8-6452692ed1d1)

There's still a blue tinge, it could be because of the linearization and clipping I performed in preprocessing, it's something I can look into.

Next, in the preprocessing step, I converted all images to log space.
<img width="1068" alt="Screenshot 2023-10-13 at 8 59 48 PM" src="https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/assets/106343437/49f78143-e857-4c5a-a56f-ef81e99a092b">

Trained the model on log images, and tested them on log preprocessed test images. (The same set from above)
These are my metrics:

Mean: 12.422635079639237<br/>
Median: 10.178940428807907<br/>
Trimean: 10.690228426911446<br/>
Average of the Lowest 25%: 3.9815587206612615<br/>
Average of the Highest 25%: 24.17618491362277<br/>

As evident, these values are nowhere near State of the art.

## Data Augmentation

I've attempted to improve metrics by performing Data augmentation. Specifically, I've multiplied each channel of the PNG image by a randomly generated value, making sure the image doesn't get saturated.
The idea is to look at minimum and maximum values in each channel and calculate the range of a multiplier so that the resulting pixel value isn't saturating the image.

![image](https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/assets/106343437/b0f05d3f-5186-4f58-9a27-7eba18de052f)

I've performed this operation for Illumination values as well. From the Von Kries model, my intuition is that every randomly generated coefficient multiplied by the raw image should divide the corresponding illumination values.

<img width="663" alt="image" src="https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/assets/106343437/75fe1b44-aadb-45ab-9b6c-96a2af83adc5">

With this hypothesis, I created a set of 600 new images, with 600 target values. I've trained the same MobileNetV2 model for 200 epochs, and these are the metrics I got.

![image](https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy/assets/106343437/1389db87-e2b2-4f23-8ad5-e4f6e7c23558)


### References : 

https://ipg.fer.hr/ipg/resources/color_constancy<br/>
Work done by Bill Collins on Color constancy: https://wiki.khoury.northeastern.edu/display/~barrelchester/Assignment+2+-+Color+Constancy
Research meeting with Professor Bruce Maxwell.<br/>

A. Gijsenij, T. Gevers and J. van de Weijer, "Computational Color Constancy: Survey and Experiments," in IEEE Transactions on Image Processing, vol. 20, no. 9, pp. 2475-2489, Sept. 2011, doi: 10.1109/TIP.2011.2118224.<br/>




