# CNNs to stock prices
Since CNNs are really good at classifying images, if we convert a time series to an image, will it be able to predict the next pattern in that series?

**First try**: Using plain CNNs (pre-trained resnet) to predict time series of HKEX stocks by first converting it into an image into granular angular fields (GAFs).

**Second try (current)**: Converting price series into fractionally differentiated series to preserve memory while producing stationary series, then converting that into an image into GAFs
