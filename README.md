# GAN Metrics

This repo contains information about IS and FID score.

Note that if we want to report IS and FID score in papers, we must use the official TF implementaion as noted by many [[1]](https://github.com/mseitzer/pytorch-fid/blame/master/README.md#L12) [[2]](https://github.com/ajbrock/BigGAN-PyTorch/blob/master/inception_utils.py#L8): 

* [Offical IS: openai/improved-gan](https://github.com/openai/improved-gan)
* [Official FID: bioinf-jku/TTUR](https://github.com/bioinf-jku/TTUR)



### Inception Score (IS)

* Assumption
  * MEANINGFUL: The generated image should be clear, the output probability of a classifier network should be [0.9, 0.05, ...] (largely skewed to a class). $p(y|\mathbf{x})​$ is of __low entropy__.
  * DIVERSITY: If we have 10 classes, the generated image should be averagely distributed. So that the marginal distribution $p(y) = \frac{1}{N} \sum_{i=1}^{N} p(y|\mathbf{x}^{(i)})$ is of __high entropy__.
  * Better models: KL Divergence of $p(y|\mathbf{x})$ and $p(y)$ should be high.
* Formulation
  * $\mathbf{IS} = \exp (\mathbb{E}_{\mathbf{x} \sim p_g} D_{KL} [p(y|\mathbf{x}) || p(y)] )$
  * where
    * $\mathbf{x}$ is sampled from generated data
    * $p(y|\mathbf{x})​$ is the output probability of Inception v3 when input is $\mathbf{x}​$
    * $p(y) = \frac{1}{N} \sum_{i=1}^{N} p(y|\mathbf{x}^{(i)})$ is the average output probability of all generated data (from InceptionV3, 1000-dim vector)
    * $D_{KL} (\mathbf{p}||\mathbf{q}) = \sum_{j} p_{j} \log \frac{p_j}{q_j}$, where $j$ is the dimension of the output probability.

* Explanation
  * $p(y)$ is a evenly distributed vector
  * larger $\mathbf{IS}​$ score -> larger KL divergence -> larger diversity and clearness
* Reference
  * Official TF implementation is in [openai/improved-gan](https://github.com/openai/improved-gan)
  * Pytorch Implementation: [sbarratt/inception-score-pytorch](https://github.com/sbarratt/inception-score-pytorch)
  * TF seemed to provide a [good implementation](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/gan/python/eval/python/classifier_metrics_impl.py)
  * [scipy.stats.entropy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.entropy.html)
  * [zhihu: Inception Score 的原理和局限性](https://zhuanlan.zhihu.com/p/54146307)
  * [A Note on the Inception Score](https://arxiv.org/abs/1801.01973)



### Fréchet Inception Distance (FID)

* Formulation
  * $\mathbf{FID} = ||\mu_r - \mu_g||^2 + Tr(\Sigma_{r} + \Sigma_{g} - 2(\Sigma_r \Sigma_g)^{1/2})​$
  * where
    * $Tr$ is [trace of a matrix (wikipedia)](https://en.wikipedia.org/wiki/Trace_(linear_algebra))
    * $X_r \sim \mathcal{N}(\mu_r, \Sigma_r)$ and $X_g \sim \mathcal{N}(\mu_g, \Sigma_g)$ are the 2048-dim activations  the InceptionV3 pool3 layer
    * $\mu_r$ is the mean of real photo's feature
    * $\mu_g$ is the mean of generated photo's feature
    * $\Sigma_r$ is the covariance matrix of real photo's feature
    * $\Sigma_g$ is the covariance matrix of generated photo's feature

* Reference
  * Official TF implementation: [bioinf-jku/TTUR](https://github.com/bioinf-jku/TTUR)
  * Pytorch Implementation: [mseitzer/pytorch-fid](https://github.com/mseitzer/pytorch-fid)
  * TF seemed to provide a [good implementation](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/gan/python/eval/python/classifier_metrics_impl.py)
  * [zhihu: Frechet Inception Score (FID)](https://zhuanlan.zhihu.com/p/54213305)
  * [Explanation from Neal Jean](https://nealjean.com/ml/frechet-inception-distance/)