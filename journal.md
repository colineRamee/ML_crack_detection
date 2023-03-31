# Summary of the approach
Found project idea on Matlab's website. Link to dataset that had been used in an old paper that trained a tree to recognize cracks.

Started by getting familar with the data. Played a little bit with OpenCV. Canny edge detection performs pretty Ok on some samples. Would require some tuning and might not be super robust in the end. 

Ground truth (boundary and semantic segmentation) is available, so pretty good for a ML approach apart from the small amount of data (only 118 labeled images) and the fact that all the images have crack in them (so no negative examples out of the box). That can easily be addressed by splitting the images.

We'll want to save all these new images and labels to a folder. Not sure what's the best structure... Probably take a look at keras to see what's expected for the image tensor...
Also we'll also want to resize the image so that they all have the same size that is compatible with and probably convert everything to greyscale.
Great resources available at: https://developers.google.com/machine-learning/practica/image-classification
Take a look at the colab for snippets of code and useful functions in keras/tensorflow framework
Note that the data augmentation can be done as part of the preprocessing pipeline directly. So let's just split images manually and otherwise use the built-in functionalities to manipulate the images.

Google ML course on image processing is pretty good and has google colab examples which help getting around keras and tensorflow. Tensorflow and keras are a bit surprising to use at first. Just have to remember that contrary to scikit where you just load everything in memory and execute step by step, tensorflow is like JAX you setup the steps/pipeline and it will optimize at runtime on the end to end graph of operations (allowing it to compute gradient and other useful things). The Google colab had outdated functions on loading data and building the model, required a bit of tinkering to get it working-ish.

Potential hiccup: computer has AMD GPU, might be a problem to train (CPU should work though).

Some of the transformations really distort the image to the point where I can't tell whether there is a crack in it or not. Adding layer degrade performance, maybe it's a vanishing gradient problem? Tensorboard looks like a useful tool to monitor what's happening. Just need a bit of work to interpret the visualizations. You access the dashboard through the browser at http://localhost:6006 (there is also an integration with jupyter notebook, but in the browser is a better experience). It's a pretty neat tool although there are some gotchas (curves can be smoothed, giving a false impressionwhen the number of epochs is low).
Neat viz: https://stanford.edu/~shervine/teaching/cs-230/cheatsheet-convolutional-neural-networks

Things I've played with: 
* data augmentation. I've been pretty conservative because some transformations make the crack invisible and I was getting bad performance, but there were other issues at the same time
* learning rate, the default one is pretty good
* number of layers
* dropout rate

Some issues:
* overfitting: learns the training set
* no fitting: vanishing gradient maybe? 

Some performance obtained during training
|Number of epochs | Network | Training accuracy | Validation accuracy | Remarks |
|--|--|--|--|--|
| 20 | Two CNN layer (16,32), dropout 0.4 | 92% | 74% ||
| 20 | Same as above additional data augmentation | 95% | 66% ||
| 30 | Three CNN layer (16,32, 64), dropout 0.4 | 9% | 77% | slower convergence |

Overall the performance kind of plateaus on the validation set . The original dataset is not that big: 118 pictures split 80/20 for training/validation. That could be a reason.