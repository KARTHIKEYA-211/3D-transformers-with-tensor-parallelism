Profiling:

As a final step, we can profile a larger model to see the performance of the 3D parallelism and compare different configurations. We recreate the model from the tensor parallelism tutorial with around 1 billion parameters. We use the same input batch size of 128, sequence length 1024, and vocabulary size 32k. We run the experiments on a single node with 8 A5000 GPUs, each having 24GB memory and having an NVLink between pairs of GPUs with 60GB/s communication bandwidth.

We first use the same 3D parallelism configuration as in the config up, using a 2x2x2 grid. We then profile the model and find a step time of 2.9 seconds. This is slower than the pure tensor parallelism model at 2.6 seconds. This is because the pipeline axis adds additional communication between devices, which are not well connected in our system and requires an additional microbatch of compute. In terms of memory, we only use 8.5GB per device, which is well within the 24GB memory of the A5000:

The largest array are the output logits of size (64, 256, 32000) (batch size 128 split over 2 data devices, sequence length 1024 split over 4 tensor and pipeline devices). Further, it is in float32 precision for numerical stability, which results in 2GB per device for this single array. The other arrays are much smaller and well within the memory limits. This highlights the importance of switching the parallelism strategies in the output to reduce the memory requirements.

Conclusion:

In this tutorial, we have combined the techniques we have implemented for data, pipeline and tensor parallelism to enable 3D parallelism. We have seen how easy it is in JAX to combine the different parallelism strategies using our previous implementations, and experiment with different 3D parallelism configurations. We have also seen how to profile the performance of the 3D parallelism and compare different configurations.
