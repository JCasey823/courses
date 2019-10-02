Artificial Intelligence, Fall 2019

Alvin Grissom II, Ursinus College


# Programming Assignment 2.1

This exercise is designed to give you practice implementing a simple Keras program.  Keras a library for implementing machine learning architectures quickly.

For this exerise, we're going to classify the MNIST dataset.  MNIST consists of a number of handwritten numbers from 0-9.  We're going to create a perceptron algorithm to classify them.  Fortunately, Keras already provides the MNIST data as a Python library.  In the future, we will have to read the files ourselves.

## Part 1: Linear Perceptron

Let's implement the linear perceptron in Keras.  First, we need to import some libraries.  Numpy is our numerical library.  The `Sequential` library allows us to build a neural network by stacking layers together.  We will come back to layers later.  

    import numpy as np
    from keras.datasets import mnist
    from keras.models import Sequential
    from keras.optimizers import SGD
    from keras.layers import Dense

### Load the data.
Recall that, in machine learning, the features of an example are usually called **x** and the set of labels/classes is usually called *Y*.  We have four Python variables here for the set of all examples and labels of our training and test data, respectively.

    (x_train, y_train), (x_test, y_test) = mnist.load_data()
        
### Understanding and Formatting the Data
![MNIST Image](https://upload.wikimedia.org/wikipedia/commons/2/27/MnistExamples.png "MNIST Image")

MNIST images are grayscale, consisting of 28 x 28 = 784 pixel values in [0,255], representing the shade of a given pixel.  We can normalize these values to the range [0,1] by dividing the values by 255 if we like.  (This is left up to you to try).

Let's flatten them to a 1D vector of length 784.

    x_train = x_train.reshape(60000, 784)
    x_test = x_test.reshape(10000, 784)
    x_train = x_train.astype('float32')
    x_test = x_test.astype('float32')


We can then have one feature for each pixel.  This is our *x* feature vector.  Our Y = {0, 1, 2, ..., 9}, representing the digit.  We can use a **one hot vector** encoding for this.  Keras does this for us.  We want to represent each of these classes as a *category* (or class label), not as a numeric value.

    y_train = keras.utils.to_categorical(y_train, num_classes)
    y_test = keras.utils.to_categorical(y_test, num_classes)

### Build the Model

The `Sequential` interface of Keras allows you to stack layers with simple function calls.

    model = Sequential()

We want a basic linear perceptron that has one feature for every pixel, with all of the features initialized to 0.  We want to find the best weights for our model with stochastic gradient descent (SGD) with a learning rate of 0.01.  We have ten classes.  Our input dimension needs to be the same as the number of features. Our **loss function** is mean squared error (MSE).  This can be changed.

    model.add(Dense(10, activation='linear', input_shape=(784,)))
   
    model.compile(loss = "mse", optimizer = SGD(lr = 0.01),
    metrics=['accuracy'])


### Train the Model and Track Its Progress
The `fit` function trains our model on the `x_train` and `y_train` data for a certain number of iterations (epochs).  The **validation data** is some *held-out* data that tests our model's accuracy at each epoch.  The batch size determines how many examples we consider at one time to modify our parameters.  You'll have to set these variables.  A reasonable batch size to start with is 128, with 20 or so epochs.

After taining, we **evaluate** to get our final accuracy on the test data.

    history = model.fit(x_train, y_train,
                    batch_size=batch_size,
                    epochs=epochs,
                    verbose=1,
                    validation_data=(x_test, y_test))
    score = model.evaluate(x_test, y_test, verbose=0)
    print('Test loss:', score[0])
    print('Test accuracy:', score[1])
    
 How did it do?

## Part 2: Improve the Model
 
Try to increase the accuracy of your model:  Here are some things to try.  This is not an exhaustive list.  Different combinations of strategies may give surprising results.  
 
 1.  Normalize your training and test data to [0,1] by dividing by 255.
    For example:
        `x_train /= 255`
 2. Replace a linear activation function with a soft ('logistic', 'softmax', etc.) function or some other one, such as ReLU.  You can find more in the Keras documentation.  Why might this help?
 3. Change the learning rate.
 4. (For the adventurous) Add more layers or dropout.

Write and submit a **short** report -- a table would be useful -- showing how various modifications affected your accuracy and why you think that this is.
