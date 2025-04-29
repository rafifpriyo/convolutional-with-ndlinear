# Experimentation: Convolutional but with NdLinear

Extracting image's feature not with CNN but NdLinear and mixing among them. The idea was derived from [MLP-Mixer](https://arxiv.org/abs/2105.01601)

See the source: [NdLinear](https://github.com/ensemble-core/NdLinear)

---

## Motivation

We know that when we heard about Processing an Image, we almost immediately think about CNN, Pooling, ViT, and so on. But is it the only technology to be considered? Why not using something simple, and hopefully easier, faster, and cheaper to train, such as NdLinear

As you've already know (or this is for you if you haven't), NdLinear can conserve the dependency between dimensional features, such as height; channel; time; indices in tokens; you name it, then why not we try to compete with the Giant to know where NdLinear stand among them, this is just the first spark of how far can we use NdLinear with/alongside the state-of-the-art technologies.

From the technical perspective, and ideas from MLP-Mixer, NdLinear can be used for inter-dimensional dependency and Convolutional can be used for global dependency

#### Self-Motivation

NdLinear is an interesting ideas to explore. Imagine simple linear architecture have a competitive performance with complex architecture like Yolo, GPT, and such

---

## Research Question

- Does NdLinear have a competitive performance with the Convolutional for image feature extraction?
- Does combining both technology using addition or concatenation makes the model better?

## Methodology

- Recreate the CNN architecture but with NdLinear as its tech
- Conserve the dimensional latent space of each layers
- Experimenting with both Convolutional and NdLinear

---

## Experimentation's Settings

- For initial steps, choose simple Image Classification Architectures, e.g. LeNet and AlexNet, to know the glimpse of NdLinear capabilities
- If possible, choose the same datasets as the above model original release, such as LeNet with MNIST and AlexNet with ImageNet
  - Because ImageNet is too big for prototyping, use [ImageNette](https://github.com/fastai/imagenette?tab=readme-ov-file#imagenette-1) instead, 10 class but with the same image resolution
- The most important part, test with several alternative integration of NdLinear with the Convolutional tech but with the same output size in every layer, in this experimentation, only try with 4 possible combinations
  - All Convolutional, Normal CNN
  - All NdLinear, Change all Convolutional with NdLinear
  - Additional, Add outputs of Convolutional and NdLinear into one latent vector as input fot the next layer
  - Concatenation, Divide the latent's channel dimension into half Convolutional and half NdLinear
- Record the parameters, time, metrics, and loss of each experimentation
- The GPU used is GPU P100 from Kaggle Notebook
- Train with Early Stopping with 30 Epoch

---

## Result

For more information, see the ipynb file

#### LeNet
| Model              | Parameter Count | Training Time   | Precision | Recall   | F1-Score |
| ------------------ | --------------- | --------------- | --------- | -------- | -------- |
| LeNetAllCNN        | 2,572           | 30 Epoch - 644s | 0.983228  | 0.983200 |	0.983195 |
| LeNetAllNdLinear   | 2,272           | 30 Epoch - 697s | 0.926775  | 0.926337 |	0.926431 |
| LeNetAdditional    | 4,844           | 30 Epoch - 723s | 0.981737	 | 0.981763	| 0.981734 |
| LeNetConcatenation | 3,496           | 30 Epoch - 727s | 0.976824  | 0.977031	| 0.976904 |



#### AlexNet
| Model                | Parameter Count | Training Time    | Normalize Time | Precision | Recall   | F1-Score |
| -------------------- | --------------- | ---------------- | -------------- | --------- | -------- | -------- |
| AlexNetAllCNN        | 3,747,200       | 9 Epoch - 480s   | 54s/epoch      | 0.685717  | 0.627879 | 0.628097 |
| AlexNetAllNdLinear   | 396,944 🔥      | 30 Epoch - 1927s | 65s/epoch      | 0.481898  | 0.468679 | 0.464338 |
| AlexNetAdditional    | 4,144,144       | 14 Epoch - 1023s | 74s/epoch      | 0.544936  | 0.480700 | 0.478146 |
| AlexNetConcatenation | 2,085,392       | 16 Epoch - 983s  | 62s/epoch      | 0.602530  | 0.573893 | 0.574325 |

---

## Argument

From LeNet experiment
- For simple classification task, NdLinear have significant decrease in performance compared to CNN (More data is needed)
- For Additional and Concatenation, NdLinear not have significat increase in performance, hence NdLinear is not provide good information to the image feature extraction (More data is needed)
- Parameter count is somewhat the same, because of the Convolutional's kernel size is small
- Training NdLinear need slightly more time compared to Convolutional with the same parameter count, but it's negligible (More data is needed)
- Not conclusive but, NdLinear is less preferable for convolutional task (More data is needed)

From AlexNet experiment
- NdLinear have way **less parameter count than Convolutional** for larger kernel, Alexnet have kernel size of 11x11 on the first layer
- Time spent for every combination almost the same, thus it's negligible
- But it's better to see the epoch number needed, NdLinear have **way harder times to converge** and **sub-optimal convergence** (More data is ok)
- Moreover, the performance of NdLinear is **less significat than Convolutional**, even if we combine both techniques. Arguably, it's because of NdLinear trapped in sub-optimal space than Convolutional, makes the NdLinear gives old-time/faraway information to the layer (Verification of sub-optimal is ok) (More data is needed)

---

## Conclusion & Future Ideas

NdLinear is somewhat ok alternative to the convolutional layer. The noticeable pros is the parameter count, especially in larger convolutional kernel size. When parameter count of convolutional need multipiplicative, NdLinear need additional from the kernel size/shape. But the downside of NdLinear is more noticeable, from the easyness to converge and/or trapped in sub-optimal solution, hence harder to use on Image Feature Extraction task (More data is needed)

Becuase of the late convergence of NdLinear, it is harder to combine NdLinear with existing technologies such as Convolutional. It is advisable to tweak with the hyperparameter or improve the NdLinear itself

Therefore, this is some ideas to work on next
- BandedNdLinear, to conserve some multidimensional aspect. I know, it's not straightly linear again, but why not?
- Having internal alpha parameter to make NdLinear more impactful to the integration, or simply made backpropagation through NdLinear tweakable
- Try it on bigger and wider CNN architecture

---

That's it, Have a Nice Day!
