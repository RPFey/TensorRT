# “Hello World” For TensorRT Using TensorFlow And Python


**Table Of Contents**
- [Description](#description)
- [How does this sample work?](#how-does-this-sample-work)
	* [Freezing a TensorFlow graph](#freezing-a-tensorflow-graph)
	* [Freezing a Keras model](#freezing-a-keras-model)
- [Prerequisites](#prerequisites)
- [Running the sample](#running-the-sample)
	* [Sample `--help` options](#sample-help-options)
- [Additional resources](#additional-resources)
- [License](#license)
- [Changelog](#changelog)
- [Known issues](#known-issues)

## Description

This sample, end_to_end_tensorflow_mnist, trains a small, fully-connected model on the [MNIST](http://yann.lecun.com/exdb/mnist/) dataset and runs inference using TensorRT.

## How does this sample work?

This sample is an end-to-end Python sample that trains a [small 3-layer model in TensorFlow and Keras](https://www.tensorflow.org/tutorials), freezes the model and writes it to a protobuf file, converts it to UFF, and finally runs inference using TensorRT.

### Freezing a TensorFlow graph

In order to use the command-line [UFF utility](https://docs.nvidia.com/deeplearning/sdk/tensorrt-api/python_api/uff/uff.html), TensorFlow graphs must be frozen and saved as `.pb` files.

In this sample, the converter displays information about the input and output nodes, which you can use to the register inputs and outputs with the parser. In this case, we already know the details of the input and output nodes and have included them in the sample.

### Freezing a Keras model

You can use the following sample code to freeze a Keras model.
```
def save(model, filename):
	# First freeze the graph and remove training nodes.
	output_names = model.output.op.name
	sess = tf.keras.backend.get_session()
	frozen_graph = tf.graph_util.convert_variables_to_constants(sess, sess.graph.as_graph_def(), [output_names])
	frozen_graph = tf.graph_util.remove_training_nodes(frozen_graph)
	# Save the model
	with open(filename, "wb") as ofile:
		ofile.write(frozen_graph.SerializeToString())
```

## Prerequisites

1. Install the dependencies for Python.
	-   For Python 2 users, from the root directory, run:
		`python2 -m pip install -r requirements.txt`

	-   For Python 3 users, from the root directory, run:
		`python3 -m pip install -r requirements.txt`

On PowerPC systems, you will need to manually install TensorFlow using IBM's [PowerAI](https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.0/navigation/pai_install.htm).

On Jetson boards, you will need to manually install TensorFlow by following the documentation for [Xavier](https://docs.nvidia.com/deeplearning/dgx/install-tf-xavier/index.html) or [TX2](https://docs.nvidia.com/deeplearning/dgx/install-tf-jetsontx2/index.html).

2. Install the UFF toolkit and graph surgeon; depending on your TensorRT installation method, to install the toolkit and graph surgeon, choose the method you used to install TensorRT for instructions (see [TensorRT Installation Guide: Installing TensorRT](https://docs.nvidia.com/deeplearning/sdk/tensorrt-install-guide/index.html#installing)).

## Running the sample

1.  Run the sample to train the model and write out the frozen graph:
	```
	mkdir models
	python model.py
	```

2.  Convert the `.pb` file to `.uff` using the convert-to-uff utility:
	`convert-to-uff models/lenet5.pb`

	Depending on how you installed TensorRT, this utility may also be located in `/usr/lib/python2.7/dist-packages/uff/bin/convert_to_uff.py` or `/usr/lib/python<PYTHON3 VERSION>/site-packages/uff/bin/convert_to_uff.py`.

3.  Create a TensorRT inference engine from the UFF file and run inference:
	`python sample.py [-d DATA_DIR]`

	**Note:** If the TensorRT sample data is not installed in the default location, for example `/usr/src/tensorrt/data/`, the data directory must be specified.
	For example: `python sample.py -d /path/to/my/data/`.

4.  Verify that the sample ran successfully. If the sample runs successfully you should see a match between the test case and the prediction.
	```
	Test Case: 2
	Prediction: 2
	```

### Sample --help options

To see the full list of available options and their descriptions, use the `-h` or `--help` command line option. For example:
```
usage: sample.py [-h] [-d DATADIR]

Runs an MNIST network using a UFF model file

optional arguments:
 -h, --help            show this help message and exit
 -d DATADIR, --datadir DATADIR
                       Location of the TensorRT sample data directory.
                       (default: /usr/src/tensorrt/data)
```

# Additional resources

The following resources provide a deeper understanding about training and running inference in TensorRT using Python:

**Model**
- [TensorFlow/Keras MNIST](https://www.tensorflow.org/tutorials)

**Dataset**
- [MNIST database](http://yann.lecun.com/exdb/mnist/)

**Documentation**
- [Introduction To NVIDIA’s TensorRT Samples](https://docs.nvidia.com/deeplearning/sdk/tensorrt-sample-support-guide/index.html#samples)
- [Working With TensorRT Using The Python API](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#python_topics)
- [NVIDIA’s TensorRT Documentation Library](https://docs.nvidia.com/deeplearning/sdk/tensorrt-archived/index.html)

# License

For terms and conditions for use, reproduction, and distribution, see the [TensorRT Software License Agreement](https://docs.nvidia.com/deeplearning/sdk/tensorrt-sla/index.html) documentation.


# Changelog

February 2019
This `README.md` file was recreated, updated and reviewed.


# Known issues

There are no known issues in this sample.
