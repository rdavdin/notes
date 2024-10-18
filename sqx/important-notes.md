Generally speaking:

every indicator has to implement an **OnBarUpdate()** method – this is where the actual indicator algorithm is.
every signal has to implement an **OnBlockEvaluate()** method that returns true or false