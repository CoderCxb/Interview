# Tapable
Webpack使用的一个发布订阅的库

### hook

###### sync 
 - SyncHook
 - SyncBailHook
 - SyncWaterfallHook
 - SyncLoopHook


###### Async
 - Parallel(并行)
    - AsyncParallelHook
    - AsyncParallelBailHook
 - Series(串行)
    - AsyncSeriesHook
    - AsyncSeriesBailHook
    - AsyncSeriesWaterfallHook

###### 注
 - sync开头为同步hook
 - async开头为异步hook
 - bail: 当有返回值时, 就不会执行后续的事件
 - loop: 当返回值不为undefined时, 就会反复执行该事件
 - waterfall: 当返回值不为undefined时, 会将这次return的结果作为下次事件的参数