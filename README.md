# Weighted_Softmax_Loss
Weighted Softmax Loss Layer for Caffe

Tested on BVLC/caffe 1.0 and NVIDIA/caffe 0.15.

## Installation:

1. Edit `src/caffe/proto/caffe.proto`, go to line `1201` and edit `SoftmaxParameter` as follows:

```
// Message that stores parameters used by SoftmaxLayer, SoftmaxWithLossLayer
message SoftmaxParameter {
  enum Engine {
    DEFAULT = 0;
    CAFFE = 1;
    CUDNN = 2;
  }
  optional Engine engine = 1 [default = DEFAULT];

  // The axis along which to perform the softmax -- may be negative to index
  // from the end (e.g., -1 for the last axis).
  // Any other axes will be evaluated as independent softmaxes.
  optional int32 axis = 2 [default = 1];
  optional float pos_mult = 3 [default = 1];
  optional int32 pos_cid = 4 [default = 1];
}
```

2. Put `weighted_softmax_loss_layer.hpp` into `include/caffe/layers`.

3. Put `weighted_softmax_loss_layer.cpp` and `weighted_softmax_loss_layer.cu` into `src/caffe/layers`.

4. Compile Caffe.

## Usage
- In prototxt, use the following syntax:
```
layer {
  name: "loss"
  type: "WeightedSoftmaxWithLoss"
  bottom: "fc_end"
  bottom: "label"
  top: "loss"
  softmax_param {
    pos_cid: 1
    pos_mult: 2.0
  }
}
```

where `pos_cid` is the class id (starts with 0) to be weighted and `pos_mult` is the weight given to that class.

## Credit
Originally by @shicai, fix to cpu_backwards proposed by @JayYangSS.
I changed it to be compatible with newer versions of Caffe and included the cpu_backwards fix.
