# Learning Time Series Shapelets

## Abstract
<p align="justify">
Shapelets are discriminative sub-sequences of time series that best predict the target variable. For this reason, shapelet discovery has recently attracted considerable interest within the time-series research community. Currently shapelets are found by evaluating the prediction qualities of numerous candidates extracted from the series segments. In contrast to the state-of-the-art, this paper proposes a novel perspective in terms of learning shapelets. A new mathematical formalization of the task via a classiﬁcation objective function is proposed and a tailored stochastic gradient learning algorithm is applied. The proposed method enables learning nearto-optimal shapelets directly without the need to try out lots of candidates. Furthermore, our method can learn true top-K shapelets by capturing their interaction. Extensive experimentation demonstrates statistically signiﬁcant improvement in terms of wins and ranks against 13 baselines over 28 time-series datasets.
</p>

<p align="center">
  <img width="1000" src="./learning_shapelets.png">
</p>

A PyTorch implementation of learning shapelets from the paper
> Josif Grabocka, Nicolas Schilling, Martin Wistuba, and Lars Schmidt-Thieme. 2014. Learning time-series shapelets. In Proceedings of the 20th ACM SIGKDD international conference on Knowledge discovery and data mining (KDD '14). Association for Computing Machinery, New York, NY, USA, 392–401.

See the [paper](https://doi.org/10.1145/2623330.2623613) for a more detailed description.

## How to use

```python
loss_func = nn.CrossEntropyLoss()
learning_shapelets = LearningShapelets(len_ts=len_ts,
                                       shapelets_size_and_len=shapelets_size_and_len,
                                       in_channels=n_channels,
                                       num_classes=num_classes,
                                       loss_func=loss_func,
                                       to_cuda=True,
                                       verbose=1,
                                       dist_measure=dist_measure)

# (Optionally) Initialize shapelet weights, the original paper uses k-Means
# otherwise the shapelets will be initialized randomly.
# Note: This implementation does not provide an initialization strategy other
# than random initialization.
learning_shapelets.set_shapelet_weights(weights)

# Initialize an optimizer.
# Make sure to first set the weights otherwise the model parameters will have changed.
optimizer = optim.Adam(learning_shapelets.model.parameters(),
                       lr=lr,
                       weight_decay=wd)
learning_shapelets.set_optimizer(optimizer)

# Train model.
losses = learning_shapelets.fit(X_train,
                                y_train,
                                epochs=2000,
                                batch_size=256,
                                shuffle=False,
                                drop_last=False)

# Extract the learned shapelets.
shapelets = learning_shapelets.get_shapelets()

# Shapelet transform a dataset.
shapelet_transform = learning_shapelets.transform(X_test)

# Make predictions.
predictions = learning_shapelets.predict(X_test)
```

See the [demo](https://github.com/benibaeumle/Learning-Shapelets/blob/main/demo/demo.ipynb) for a detailed example.

### Parameters

* `len_ts` : **int**  
  The length of the time series.
* `shapelets_size_and_len` : **dict(int: int)**  
  The keys are the length of the shapelets and the values the number of shapelets of
  a given length, e.g. {40: 4, 80: 4} learns 4 shapelets of length 40 and 4 shapelets of
  length 80.   
* `in_channels` : **int**  
  the number of channels of the time series.
* `num_classes` : **int**  
  the number of output classes.
* `loss_func` : **torch.nn**  
  the loss function.
* `dist_measure` : **euclidean**, **cross-correlation**, or **cosine**  
  the distance measure to use to compute the distances between the shapelets.
  and the time series.
* `to_cuda` : **bool**  
  Performs computations on GPU is true. Needs pytorch with GPU support.
* `verbose` : **bool**  
  Monitors training loss if set to true.

### Methods

* `fit(X, Y, epochs=1, batch_size=256, shuffle=False, drop_last=False)`: Learn time-series shapelets. If `shuffle` is true, the batches are shuffled after each epoch
  If `drop_last` is true, the last batch will be ignored if it is smaller than the batch size.
* `transform(X, Y)`: Perform shapelet transform
* `fit_transform(X, Y, epochs=1, batch_size=256, shuffle=False, drop_last=False)`: Perform `fit` followed by `transform`.
* `set_optimizer(optimizer)`: Set an optimizer for training.
* `set_shapelet_weights(weights)`: Initialize shapelets.
* `set_shapelet_weights_of_block(i, weights_block)`: Initialize the weights of a shapelet block, blocks are sorted ascending according to the length of the shapelets
* `update(x, y)`: Perform a single gradient update step with respect to batch (x, y).
* `predict(X)`: Classify X.
* `get_shapelets()`: Get the learned shapelets.
* `get_weights_linear_layer()`: Get the weights and biases of the logistic regression layer

## License

Released under MIT License. The code was developed by Benedikt Bäumle.

## Credits

The code was developed with the aid of the following libraries:

* [PyTorch](https://pytorch.org/)
* [NumPy](https://numpy.org/)
* [tqdm](https://github.com/tqdm/tqdm)
