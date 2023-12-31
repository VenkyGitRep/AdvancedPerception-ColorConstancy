# AdvancedPerception-ColorConstancy
Assignment: Color Constancy, Shadow Removal, or Intrinsic Imaging

Submitted by: Venkateshwaran Sundar (sundar.ve@northeastern.edu)

Github : https://github.com/VenkyGitRep/AdvancedPerception-ColorConstancy

Comparison of color constancy between linear and log images

I've attempted to compare computational color constancy linear and log images using the Cube+ dataset.
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

Mean: 5.207108231765006
Median: 1.061212293545509
Trimean: 2.01843737570492
Average of the Lowest 25%: 0.30633756652674365
Average of the Highest 25%: 17.28117165474485

In comparison, when the loss function was Mean Squared error, this was the metrics for Angular error:
Mean: 6.937295205977416
Median: 4.2797557829048225
Trimean: 4.967636278881123
Average of the Lowest 25%: 2.696353296734367
Average of the Highest 25%: 15.730027304618213

Since Trimean is a good measure of central tendency, looks like using Angular error as a loss function has paid some dividends, with Trimean reducing by 2 points.
It's evident that all metrics are better with this loss function in the model.

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

Mean: 12.422635079639237
Median: 10.178940428807907
Trimean: 10.690228426911446
Average of the Lowest 25%: 3.9815587206612615
Average of the Highest 25%: 24.17618491362277


It's evident log images perform worse in comparison to linear images, however, this is not what our expectation is(or what the theory says). I believe this can be remedied by taking a better look at preprocessing for log images and tweaking the model. I used only 50 epochs for this approach.

Summary: While my results aren't what I expected to achieve, I see there's room to optimize in the way of preprocessing and optimization.
I plan on continuing work on this, perhaps having better results for a final project.

References : 
https://ipg.fer.hr/ipg/resources/color_constancy
Work done by Bill Collins on Color constancy: https://wiki.khoury.northeastern.edu/display/~barrelchester/Assignment+2+-+Color+Constancy
Research meeting with Professor Bruce Maxwell.




