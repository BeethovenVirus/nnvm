# NNVM: Graph IR Stack for Deep Learning Systems

[![Build Status](https://travis-ci.org/dmlc/nnvm.svg?branch=master)](https://travis-ci.org/dmlc/nnvm)
[![GitHub license](http://dmlc.github.io/img/apache2.svg)](./LICENSE)

NNVM is a reusable computational graph optimization and compilation stack for deep learning systems. It provides modules to:

- Represent deep learning workloads from front-end frameworks via a graph IR.
- Optimize computation graphs to improve performance.
- Compile into executable modules and deploy to different hardware backends with minimum dependency.

NNVM is designed to add new frontend, operators and graph optimizations in a decentralized fashion without changing the core interface. NNVM is part of [TVM stack](https://github.com/dmlc/tvm). NNVM compiler toolchain can target hardware backends supported by TVM.
The compiled module can be deployed to server, mobile, embedded devices and browsers with minimum dependency, in languages including c++, python, javascript, java, objective-c.

The following code snippet demonstrates the general workflow of nnvm compiler toolchain.

```python
import tvm
from tvm.contrib import graph_runtime, rpc
import nnvm.frontend
import nnvm.compiler

# get model from frameworks
# change xyz to supported framework name.
graph, params = nnvm.frontend.from_xyz(...)

# optimize and compile the graph to get a deployable module
# target can be "opencl", "llvm", "metal" or any target supported by tvm
target = "cuda"
graph, lib, params = nnvm.compiler.build(
   graph, target, shape={"data", data_shape}, params=params)

# deploy and run on gpu(0)
module = graph_runtime.create(graph, lib, tvm.gpu(0))
module.set_input(**params)
output = tvm.nd.empty(out_shape, ctx=tvm.gpu(0))
for data_array in dataset:
    module.set_input("data", data_array)
    module.run()
    module.get_output(0, output)

# deploy to remote mobile/rasp/browser with minimum tvm rpc runtime
# useful for quick experiments on mobile devices
remote = rpc.connect(remote_host, remote_port)
lib.export_library("mylib.so")
remote.upload("mylib.so")
rlib = rpc.load_module("mylib.so")
# run on remote device
rmodule = graph_runtime.create(graph, rlib, remote.gpu(0))
rmodule.set_input(**params)
rmodule.run()
```

## Links
- [TinyFlow](https://github.com/tqchen/tinyflow) on how you can use  NNVM to build a TensorFlow like API.
- [Apache MXNet](http://mxnet.io/) uses NNVM as a backend.
