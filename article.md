# Introduction

> “History doesn’t repeat itself but it often rhymes.” Mark Twain

After learning about how powerful Convolutional Neural Networks (CNNs) are at image recognition, I wondered if algorithms could read stock market charts better than a human chartist, whose job is to discover chart patterns and profit from them. We know that CNNs are better at classifying ImageNet pictures better than humans, but can they read and find patterns better than other market players?


## 1. Choosing the right market / dataset
I think it’s logical that technical analysis will not work in markets that quant funds trade in — any possible gains from a funny chart pattern or [insert sophisticated sounding term] has to have been squeezed dry by any decent quant, but I’m fascinated to see if simple inefficiencies remain where quants don’t go due to limited capacity. Hence I looked for a market outside of the top global exchanges but was still accessible — the Warsaw stock exchange was one such example.

## 2. Perils of train-test split for time series
It is easy to split datasets using standard ML packages, but time-series have to be treated carefully, otherwise data leakage occurs and create optimistic in-sample results that don’t work out-of-sample. If your random train-test split makes your model learn how to fill the gaps below, you are essentially using the future to predict the past!

I followed Rachel Thomas’ recommendation to split up the data in a way that will not make the model predict the past, for important nuances when choosing validation data you should read her post.

## 3. Imaging Time Series using Recurrence Plots
The most direct way to feed inputs to our CNN would be to use a standard price/volume chart, something like this:



However, since the way CNNs process and learn insights from data is likely different from humans, it may be useful to represent the time series in a different form. Also, since images have 3 channels, it’s possible to pack each channel with a different metric to provide more information to our algorithm. This useful library has a few functions that implements SOTA image representations of time series.

One such transformation is Gramian Angular Fields (GAFs), which converts a time series into a “polar co-ordinate system instead of the typical Cartesian coordinates.” [0]. Another such representation are Recurrence Plots, which “display important and easily interpretable information about time scales which are otherwise rather inaccessible”. [1] Below are some transformed charts, looks pretty different eh? You can see the final results of my experiments at the end of the page.


Recurrence Plot transformations
## 4. Fractional Differentiation — balancing memory and stationarity
“Stationarity is a necessary, non-sufficient condition for the high performance of an ML algorithm. The problem is, there is a trade-off between stationarity and memory. We can always make a series more stationary through differentiation, but it will be at the cost of erasing some memory, which will defeat the forecasting purpose of the ML algorithm.” Marcos Lopez de Prado[2]

Figure 1: Memory vs Time [2]
A standard engineered feature when working with price series is the return series. Lopez[2] writes that the problem with return series is the loss of memory. Figure 1 essentially shows memory preserved against time. For return series, i.e. an ‘integer 1’ differentiation, the memory of the series loses memory of past values after the first period. But we can see for fractional differentiation values, the memory of historical values persist, which helps in predictions.

The trick then is to find a fraction where differentiation that is below 1, but still achieves stationarity — an ADF test can check if stationarity is achieved for a given fraction.

## 5. Coordconv— Optionality of Translation Invariance
One key reason CNNs are so good at classifying images is translation invariance, i.e. the output is invariant to the input changing slightly. If you’re trying to classify a cat that is not always in the same spot, it will still identify the cat.


Source: What is wrong with Convolutional neural networks
But for time series, a spike earlier in time has a different meaning than a spike right before prediction time, thus we may want to throw out translation invariance if it’s useful — this optionality is possible using Coordconv[3]. A Coordconv layer includes 2 layers of i and j coordinates which lets the CNN know where each pixel is at. If the algorithm decides the coordinates are useful, the weights will reflect that, hence there is only upside with using Coordconv! The authors found that adding Coordconv to a variety of CNN tasks almost always improved performance.


Source: An intriguing failing of convolutional neural networks and the CoordConv solution [3]
Results and further work
I’ve used CNNs to forecast time series by representing the time series data as images. The CNN has a relatively simple binary classification task — decide whether closing prices the next day will be positive or not.

I focused on precision, because we lose money when we make a bet on a false positive. The best score was 64% using a fractionally differentiated series(d=0.3) with GAF imaging. The notebooks I ran to get the best results are here.


Next, I will look at improving the CNN architecture to better capture temporal relationships — i.e. Wavenets! Excited to understand and test it out.

## Special Mention: fast.ai
I’m learning quite a lot from my masters in AI, but I’ve learnt equally as much, if not more about practical deep/machine learning from fast.ai. The fast.ai library is packed with state of the art deep learning techniques that speed up training and their default settings produce amazing results which frees up the user to focus on other parts of their experiments. The forums are really active as well and most of the ideas above came from folks in this thread.

## References
[0] Z. Wang and T. Oates, “Encoding Time Series as Images for Visual Inspection and Classification Using Tiled Convolutional Neural Networks.”AAAI Workshop (2015).

[1] J.-P Eckmann and S. Oliffson Kamphorst and D Ruelle, “Recurrence Plots of Dynamical Systems”. Europhysics Letters (1987).

[2] Marcos Lopez de Prado, “Advances in Financial Machine Learning”. 2018

[3] Rosanne Liu, Joel Lehman, Piero Molino, Felipe Petroski Such, Eric Frank, Alex Sergeev, and Jason Yosinski, “An intriguing failing of convolutional neural networks and the CoordConv solution”. 2018.
