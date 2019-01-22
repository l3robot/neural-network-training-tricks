# Neural Network Training Tricks
_Compilation of tricks to train a neural network_

- [Organisation](#organisation)
- [Starting](#starting)
- [Design](#design)
- [Debugging](#debugging)


## <a name="organisation"></a>Organization

It is always a smart idea to organize your environment to avoid catastrophic losses of data, results, training time. Here are some advices:

- Separate your data, results and code.
- Use a terminal multiplexer like tmux or screen to never loose your current session. Espacially when you are working remotely.
- Code a checkpoint manager and checkpoint your current state of training to never loose some crucial training time.
- Code an experiment manager to keep all your testing hyperparameters. Never forget what you have done in a certain experiment.
- Use [tensorboardX](https://github.com/lanpa/tensorboardX), it is very easy to setup in an experiment and lets you visualize the training.

## <a name="starting"></a>Starting

When you train a new model, start from humble beginnings:

- Train your network on a toy dataset first, MNIST for instance.
- You can even train on a low resolution MNIST first. This allows you to train without a lot of memory need and test multiple hyperparameters on a single GPU.

## <a name="design"></a>Design

Design choice matters more than you could think in neural networks. In fact, design matters everywhere. Design choices often come from intuition and experience. Some of the advices below have been studied in details in some papers, others not. The point here is more to give you advices that worked well in practice than to survey the litterature. We will try to link to relevant piece of paper whenever possible. Here is the list:

- Normalization:
    + If you are not using it, use it.
    + If you are using it, but the training results are quite different than the test resuls, ensure that your network is in evaluation mode.
    + If the results are still different, ensure the running stats are well approximated. The batch size can have a big important impact the approximation. For instance, if the last batch of an epoch and the number of total batches are small. Consider using InstanceNorm instead which keeps a running statistics on each item separately.
    + If your training is very slow and even stagnate at a higher point than expected, make sure you don't have normalization layers on key inference points like features inference or output inference. There are the places where you don't necessarily want a zero mean, unit variance activation.
- Gradients:
    + If you have multiple losses and you think one is taking over the other, check the magnitude of both loss activating one at a time. Adjust the loss to make the ratio closer to one or to reach a certain wanted behaviour. This is especially true if you train a network with different entry points for different losses, like in variational auto-encoder (VAE).
    + If your training stagnates, ensure that the gradient is not stopping at a certain point of the network and flows all over to the inputs. Again, this is especially true if you are training a VAE, did you forget the reparametrization trick!

## <a name="debugging">Debugging

- If a certain behaviour is triggered after a certain number of steps, make sure to reduce the number of steps artifically to debug it fast and change it back after.
- When you just modified the code to test a certain behaviour, make sure to indicate it with a comment. The comment could save the future yourself searching endlessly why is the freaking model not learning anymore.
- Put some assert and elevate your error handling skills. Again, thing about the future yourself, message him. Nothing is more a pain than searching for a bug. Receiving a clear indication why the code has crashed could save you a lot of time.
