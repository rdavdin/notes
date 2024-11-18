### Indicator

- Every indicator has to implement an **OnBarUpdate()** method – this is where the actual indicator algorithm is.
- Every signal has to implement an **OnBlockEvaluate()** method that returns true or false

### 2 Important Steps
1. **Step 1**: Implement a custom indicator in SQX
- This is the 1st half part
- The custom indicator only runs in SQX 
2. **Step 2**: Add definition to the snippet to work on other platforms like MetaTrader
- add definition how this new snippet will be translated to the source code of the platform
- add implementation of this indicator also to your target platform

### Return Types of an Indicator

1. **Price** – indicator computes price and is displayed on price chart – like Bollinger Bands, Moving Average, etc.
2. **Number** – indicator computes number that is displayed on its own chart – like CCI, RSI, MACD, etc.
3. **PriceRange** – indicator computes price range (difference f two prices) – like ATR


### Data Types: DataSeries vs ChartData

- **DataSeries**: an array stores **double** values
- **ChartData**: Can get data like: Open, Close, High, Low, Volumn


### OnInit() - What is it?
