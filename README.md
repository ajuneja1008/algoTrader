# algoTrader

Description: An algorithmic trading bot implementing quantitative trading strategies based on price/volume alphas to trade Indian Equities.

Please note that I've decided to move the source code to another private repository.
If you'd like to view/collaborate on the same, please feel free to reach out. 

The trading engine is designed in a completely modular way with different layers handling different logics. The basic idea is to convert tick data listened from Zerodha Kite API to candle data and then compute some technical indicators based on that. Then the investor takes trading decisions based on that and relays it to the order manager which finally sends the order to the exchange using the same API. The investor currently also has a basic risk management built in. 

Here is a brief description of the different layers used in this trading engine.

1. <b>MarketDataListener</b>: This layer is responsible for connecting to the API and relaying the data to its subscribers.
2. <b>TickManager</b>: This layer processes the tick data and relays the output to the candle data manager of the relevant instruments. All this data is also logged on a separate thread so that we can also replay events in simulation.
3. <b>CandleGenerator</b>: This layer is responsible for processing the ticks received from the TickManager and generating candle data from that. This data is also separately logged for faster processing of simulations.
4. <b>CandleManager</b>: This is an interface layer between the investors and the CandleGenerators. Multiple investors can subscribe to this layer to get the candle data of multiple instruments.
5. <b>Indicator</b>: Abstract class to handle all indicators. Any technical indicator inherits from this class. All the logic of computing the indicators goes into this layer.
6. <b>TradingClient</b>: This is the investor layer. This subscribes to CandleManager to get candle data, then also subscribes to various indicators to get the alpha. Then it computes an opinion of trade based on inputs from different indicators as well as current risk held. This opinion is communicated to the OrderManager which then places the order.
7. <b>OrderManager</b>: This receives the opinion from the investor and sends the order to the exchange through the API. This is also responsible for letting the investor know about the state of the order, whether it got confirmed, rejected, filled etc.
8. <b>SimCandleManager</b>: Takes a file as input which contains the candle data and relays it to the investor so that sim and production pipeline of code are as similar as possible and backtesting the strategies becomes more reliable.

If you are keen to discuss the kind of trading strategies I am using, please reach out to me, don't want to disclose them on a public forum.
