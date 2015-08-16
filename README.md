# Multiview test in Caffe

Caffe is among the best presentations of convolutional nerual networks. Its competiters might be cuda-convnet, torch, theano and so on. I choose caffe to train my models because its elegent design of 'solver-net-layer-blob-syncedmem' structure and the excellent speed of trainning is also impressive.

But it seems that the multiview(or 10 view) test of network trained with transformed data is currently not available in caffe at all, even though such method of model testing would always achieve a better score as decribed by Alex Krizhevsky in the training strategy of [cuda-convnet](https://code.google.com/p/cuda-convnet/wiki/TrainingNet).

So a few lines of codes are added to realize such function.

## What's multiview test

Training a CNN could be very tricky. For example, in the task of object classification, an effective trick is training with transformed data --- in every epoch, each training image only feeds the net with a small patch of itself(which is called cropping) and the patch might also be transformed as its horizontal reflection since objects in the mirror can be easily recogonized by human. Various transformed data helps relieve overfitting of tradition BP training.

<img src="https://raw.githubusercontent.com/AkiChen/Multiview-Caffe/master/pictures/multiview_origin_pic0.png">

The input transformed data of CNN in train&test phase is shown in the graph above. Once the size of training patch is decided, transformed patch is randomly generated (both the Offset_Y and Offset_X are random numbers, the random patch is mirrored by a probability of 0.5). The training phase of caffe and cuda-convnet are almost the same when applied with cropping&mirror and both of them take the center patch of image in testing by default. Nevertheless an alternate testing method is also adopted in cuda-convnet: each sample would generate 10 different patches that its final prediction of label is acuqired by averaging the testing result of ten transformed patches as below.

<img src="https://raw.githubusercontent.com/AkiChen/Multiview-Caffe/master/pictures/multiview_origin_pic1.png">

In most cases, the multiview test performs better than only testing on the center patch and the improvement depends mainly on the cropping size that the more you crop on the corner, the more significant enhancement you will gain with multiview testing.

## How to use

You need to edit your model definition at two places. Check the `cifar10_quick_train_test.prototxt` in `examples\cifar10`.

1. Add one line in the `transform_param` of your data input layer: 

<pre>
<code>
    transform_param { 
        mean_file: "mean.binaryproto" 
        crop_size: 30 
        <strong>multi_view:true</strong>
    }
</code>
</pre>
    


Unfinished





