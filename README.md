# CNNs to stock prices
Since CNNs are really good at classifying images, if we convert a time series to an image, will it be able to predict the next pattern in that series?

First try: Using plain CNNs (pre-trained resnet) to predict time series of HKEX stocks by first converting it into an image using granular angular fields.

Second try (current): Instead of converting time series, take key metrics from technical analysis and create a feature map of rich representations of where the stock is right now.
