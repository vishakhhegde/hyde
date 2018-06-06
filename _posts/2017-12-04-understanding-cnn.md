---
layout: post
title: Understanding Convolutional Neural Networks
comments: true
img_excerpt: imgs/cnn.jpg
---

The field of statistical machine learning relies on data to estimate a function predominantly for inference and prediction. This data is assumed to be generated from a data generating process. Inference concerns with understanding the anatomy and structure of such a process. Prediction deals with predicting the outcome based on some evidence, without worrying much about the underlying mechanism causing such outcomes.

When it concerns businesses, it is important to have models that can be interpreted to some extent. The reasons are many-fold:

1. With an understanding of the model, we can hope to improve it faster. 
2. A better model can mean better user experience, better safety, improved revenue and expenditure reduction.
3. Provide stakeholders with valuable insight that can help them take meaningful actions and decisions.
4. Fix faulty data, leading to better data collection and interpretation practices which further leads to better models.

Recent developments in machine learning, like Random Forests and Deep Neural Networks make use of models that can be used well for predictions, but are hard to understand from the inference standpoint. Some of the best models for image, text and speech analytics are deep neural networks that have very high capacities compared to classical Generalised Linear Models like logistics regression. These deep neural networks are now seeing rapid adoption in various other unconventional domains as well.

Model capacity and interpretability do not always go hand in hand as shown in the following schematic diagram:

<img src="{{ site.url }}/imgs/understandingcnn/model_capacity_graph.jpg" style="align:center; margin: 0 auto; width:50%;">
<p style="text-align: center; font-style: italic; font-size: 90%;">Model intrepretability versus model capacity.</p>


There has been some ongoing research in the field of interpreting deep neural networks. However, there is no consensus yet on the best method. Most of them are field specific. Here are a few used in the field of computer vision and what they are useful for:

### Generating an image that maximises a class score
Let $$S_{c}(I)$$ be the score of class $$c$$ with respect to image I. This method tries to find $$I$$ that maximises $$S_c$$ for a class $$c$$. Repeated back-propagation (similar to one used for training for classification) can be used, where the weights are frozen, but the image is updated in each step. This gives a pattern of pixels that maximise class score. Such generated images should have patterns of the class of interest. Images that are gibberish mean that the network hasn’t learned to discriminate that class well, or that there are bad examples in the training set for that particular class.

Model capacity and interpretability do not always go hand in hand as shown in the following schematic diagram:

<img src="{{ site.url }}/imgs/understandingcnn/goose.png" style="width:10%"/> <img src="{{ site.url }}/imgs/understandingcnn/dumbbell.png" style="width:10%"/> <img src="{{ site.url }}/imgs/understandingcnn/lemon.png" style="width:10%"/>
![]({{ site.url }}/imgs/understandingcnn/goose.png)  |  ![]({{ site.url }}/imgs/understandingcnn/goose.png)

### Image-specific class saliency visualization
The gradient of the true class score with respect to the image results in a saliency map where pixels of higher intensity contribute the most to increasing the true class score. Unlike the previous method, one step of back-propagation is sufficient. They usually highlight parts of the image that are specific to the class of interest. In-fact, they have been shown to be useful for image-segmentation.

### Activation maps
The result of a convolution of a layer with a kernel. For networks with ReLU activations, the activations start out as dense blobs that gradually become sparse with more training. This technique can be used to watch out for dead-filters (filters that result in a blank activation map for all inputs) and are manifestations of high learning rates.

### Visualising filters
Visualization of convolution filters can give insights on how well the model is trained, though they are by themselves hard to interpret. Noisy filters usually mean that the network isn’t trained long enough, or is overfitting due to various reasons (small dataset, low regularization, etc).

### Finding images that affects a neuron the most
Every neuron is associated with its receptive field. Finding what in its receptive field maximally activates a neuron can shed some light on what in the image is important for a neuron. However in many cases, there is no single neuron special to a visual feature of the image.

### Finding a heat map of regions that most affect the score
Blocking a region of an image and running it through a CNN will output scores when part of the image is occluded. We can then sweep this occlusion through the image to see what part matters the most by looking at regions that result in the smallest score for the class of interest. A plot of these class scores as a function of position of occlusion produces an inverse heat map. This method can be used to verify that the CNN is placing emphasis on the right areas of the image.

### Visualising embeddings
Classification networks transform an image in the image space into a representation that can be classified using a linear classifier. In fact, the softmax function performs a high dimensional logistic regression on the transformed representation. A well separated embedding space suggests that the network has learnt to discriminate between different classes. In many cases, embeddings lie in an N-dimensional manifold (N > 3) and is therefore hard to visualise in its native form. There are many tools to map embeddings to a lower dimensional space for visualization. The most widely used method is t-SNE that requires tuning various parameters of the algorithm and can sometimes lead to confusing results. Having an intuition for how it works can greatly benefit the analysis of embeddings.

### Influence functions
One of the latest tools in the broader toolkit for understanding black-box predictions like that of a CNN or random forests. This method looks at how weights of the network and loss on a test point change when a certain training point is not used for training by using influence functions. Influence functions can also help find perturbations of an image that maximally increase the loss on a test point, giving a one to one connection between a training point and prediction on a test point. They are also shown to be useful for fixing mislabeled training examples Influence functions give exact results for convex and differentiable models. For neural networks that are highly non-convex functions, there is a good second order approximation for influence functions that is not computationally expensive to compute.

### References
1. Karen Simonyan, Andrea Vedaldi, Andrew Zisserman, Deep Inside Convolutional Networks: Visualising Image Classification Models and Saliency Maps, arXiv:1312.6034
2. Pang Wei Koh, Percy Liang, Understanding Black-box Predictions via Influence Functions, arXiv:1703.04730
3. Jost Tobias Springenberg , Alexey Dosovitskiy , Thomas Brox, Martin Riedmiller, STRIVING FOR SIMPLICITY: THE ALL CONVOLUTIONAL NET, arXiv:1412.6806
4. Visualising and understanding convolutional neural networks, Matthew D Zeiler and Rob Fergus, arXiv:1311.2901
5. Blog: http://cs231n.github.io/understanding-cnn/