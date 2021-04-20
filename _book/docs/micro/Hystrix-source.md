Hystrix方法执行与降级方法执行源码分析

主要对加了@HystrixCommand注解的方法用AOP拦截实现类HystrixCommandAspect去拦截，主要拦截执行方法如下



* publicObjectmethodsAnnotatedWithHystrixCommand(final ProceedingJoinPoint joinPoint)throwshrowable{
* //被@HystrixCommand标记的hello()方法
* Method method=getMethodFromTarget(joinPoint);
* MetaHolderFactory metaHolderFactory= ...get(HystrixPointcutType.of(method));

5MetaHolder metaHolder=metaHolderFactory.create(joinPoint);

6//准备各种材料后，创建HystrixInvokable

7HystrixInvokable invokable=HystrixCommandFactory.getInstance().create(metaHolder);8Object result;

9try{

10if(!metaHolder.isObservable()) {

11//利用工具CommandExecutor来执行具体的方法

12result=CommandExecutor.execute(invokable,executionType,metaHolder);

13. }
14. }
15. returnresult;
16. }

HystrixInvokable 只是一个空接口，没有任何方法，只是用来标记具备可执行的能力。

那 HystrixInvokable 又是如何创建的？它具体的实现类又是什么？先看

看H ystrixC om m andFactory.getInstance().create()的代码。



1publicHystrixInvokablecreate(MetaHolder metaHolder) {

2return newGenericCommand(...create(metaHolder));

* }

GenericCommand 负责执行具体的方法和fallback时的方法



* //执行具体的方法，如：OrderController的findById()

2protectedObjectrun()throws Exception{

3returnprocess(newAction() {

* @Override
* Objectexecute() {
* returngetCommandAction().execute(getExecutionType());
* }
* });
* }
10. //执行fallback方法，如：OrderController的findByIdFallback()
11. protectedObjectgetFallback() {
12. final CommandAction commandAction=getFallbackAction();
13. returnprocess(newAction() {
14. @Override
15. Objectexecute() {
16. MetaHolder metaHolder=commandAction.getMetaHolder();
17. Object[]args=createArgsForFallback(...);
18. returncommandAction.executeWithArgs(...,args);
19. }
20. });
21. }

toObservable()核心源码解析



* publicObservable<R>toObservable() {2final AbstractCommand<R>_cmd=this;

3//命令执行结束后的清理者

4final Action0 terminateCommandCleanup=newAction0() {...};

5//取消订阅时处理者

6final Action0 unsubscribeCommandCleanup=newAction0() {...};

7//重点：Hystrix核心逻辑:断路器、隔离

8final Func0<Observable<R>>applyHystrixSemantics=newFunc0<Observable<R>>() {...};

9//发射数据(OnNext表示发射数据)时的Hook

10final Func1<R,R>wrapWithAllOnNextHooks=newFunc1<R,R>() {...};

11//命令执行完成的Hook

12final Action0 fireOnCompletedHook=newAction0() {...};13//通过Observable.defer()创建一个Observable

14returnObservable.defer(newFunc0<Observable<R>>() {15@Override

16publicObservable<R>call() {

17final boolean requestCacheEnabled=isRequestCachingEnabled();

18final String cacheKey=getCacheKey();

19//首先尝试从请求缓存中获取结果

20if(requestCacheEnabled) {

21. HystrixCommandResponseFromCache<R>fromCache=(HystrixCommandResponseFromCache<R>)requestC ache.get(cacheKey);
22. if(fromCache!=null) {
23. isResponseFromCache=true;
24. returnhandleRequestCacheHitAndEmitValues(fromCache,_cmd);
25. }
26. }
27. //使用上面的Func0：applyHystrixSemantics来创建Observable
28. Observable<R>hystrixObservable=
29. Observable.defer(applyHystrixSemantics)
30. .map(wrapWithAllOnNextHooks);
31. Observable<R>afterCache;
32. //如果启用请求缓存，将Observable包装成HystrixCachedObservable并进行相关处理
33. if(requestCacheEnabled&&cacheKey!=null) {
34. HystrixCachedObservable<R>toCache=HystrixCachedObservable.from(hystrixObservable,_cmd);
35. ...
36. }else{
37. afterCache=hystrixObservable;
38. }
39. //返回Observable
40. returnafterCache
41. .doOnTerminate(terminateCommandCleanup)
42. .doOnUnsubscribe(unsubscribeCommandCleanup)
43. .doOnCompleted(fireOnCompletedHook);
44. }
45. });

![图片](https://uploader.shimo.im/f/4JA5FOPhEIvWVVKB.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

46. }


断路器、隔离核心代码applyHystrixSemantics()



* // Semantics译为语义,应用Hystrix语义很拗口，其实就是应用Hystrix的断路器、隔离特性

2privateObservable<R>applyHystrixSemantics(final AbstractCommand<R>_cmd) {

* //源码中有很多executionHook、eventNotifier的操作，这是Hystrix拓展性的一种体现。这里面啥事也没做，留了个口子，开发人员可以拓展
* executionHook.onStart(_cmd);
* //判断断路器是否开启
* if(circuitBreaker.attemptExecution()) {

7//获取执行信号

8final TryableSemaphore executionSemaphore=getExecutionSemaphore();

9final AtomicBoolean semaphoreHasBeenReleased=newAtomicBoolean(false);10final Action0 singleSemaphoreRelease=newAction0() {...};

11final Action1<Throwable>markExceptionThrown=newAction1<Throwable>() {...};

12//判断是否信号量拒绝

13if(executionSemaphore.tryAcquire()) {

14try{

15//重点：处理隔离策略和Fallback策略

16returnexecuteCommandAndObserve(_cmd)

17.doOnError(markExceptionThrown)

18.doOnTerminate(singleSemaphoreRelease)

19.doOnUnsubscribe(singleSemaphoreRelease);

20}catch(RuntimeException e) {

21returnObservable.error(e);

22. }
23. }else{
24. returnhandleSemaphoreRejectionViaFallback();
25. }
26. }
27. //开启了断路器,执行Fallback
28. else{
29. returnhandleShortCircuitViaFallback();
30. }
31. }

executeCommandAndObserve()处理隔离策略和各种Fallback



* privateObservable<R>executeCommandAndObserve(final AbstractCommand<R>_cmd) {
* final HystrixRequestContext currentRequestContext=HystrixRequestContext.getContextForCurretThread();
* final Action1<R>markEmits=newAction1<R>() {...};

4final Action0 markOnCompleted=newAction0() {...};5//利用Func1获取处理Fallback的Observable

* final Func1<Throwable,Observable<R>>handleFallback=newFunc1<Throwable,Observable<R>>()

{

* @Override
* publicObservable<R>call(Throwable t) {

9circuitBreaker.markNonSuccess();

![图片](https://uploader.shimo.im/f/VxeCN9PvxHWGFjPv.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

10. Exception e=getExceptionFromThrowable(t);
11. executionResult=executionResult.setExecutionException(e);
12. //拒绝处理
13. if(einstanceofRejectedExecutionException) {
14. returnhandleThreadPoolRejectionViaFallback(e);
15. //超时处理
16. }else if(tinstanceofHystrixTimeoutException) {
17. returnhandleTimeoutViaFallback();
18. }else if(tinstanceofHystrixBadRequestException) {
19. returnhandleBadRequestByEmittingError(e);
20. }else{
21. ...
22. returnhandleFailureViaFallback(e);
23. }
24. }
25. };
26. final Action1<Notification<?superR>>setRequestContext...
27. Observable<R>execution;
28. //利用特定的隔离策略来处理
29. if(properties.executionTimeoutEnabled().get()) {
30. execution=executeCommandWithSpecifiedIsolation(_cmd)
31. .lift(newHystrixObservableTimeoutOperator<R>(_cmd));
32. }else{
33. execution=executeCommandWithSpecifiedIsolation(_cmd);
34. }
35. returnexecution.doOnNext(markEmits)
36. .doOnCompleted(markOnCompleted)
37. //绑定Fallback的处理者
38. .onErrorResumeNext(handleFallback)
39. .doOnEach(setRequestContext);
40. }

隔离特性的处理：executeCommandWithSpecifiedIsolation()



* privateObservable<R>executeCommandWithSpecifiedIsolation(final AbstractCommand<R>_cmd) {

2//线程池隔离

3if(properties.executionIsolationStrategy().get()==ExecutionIsolationStrategy.THREAD) {4//再次使用Observable.defer(),通过执行Func0来得到Observable

5returnObservable.defer(newFunc0<Observable<R>>() {

* @Override
* publicObservable<R>call() {

8//收集metric信息

9metrics.markCommandStart(commandKey,threadPoolKey,ExecutionIsolationStrategy.THREAD);10...

11try{

12...//获取真正的用户Task

13returngetUserExecutionObservable(_cmd);

14}catch(Throwable ex) {

15returnObservable.error(ex);

16. }

![图片](https://uploader.shimo.im/f/jrL8MergFllwY3h6.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

17. ...
18. }
19. //绑定各种处理者
20. }).doOnTerminate(newAction0() {...})
21. .doOnUnsubscribe(newAction0() {...})
22. //线程隔离，绑定超时处理者
23. .subscribeOn(threadPool.getScheduler(newFunc0<Boolean>() {
24. @Override
25. publicBooleancall() {
26. returnproperties.executionIsolationThreadInterruptOnTimeout().get()&&_cmd.isCommandTimedOut.get()==TimedOutStatus.TIMED_OUT;
27. }
28. }));
29. }
30. //信号量隔离，和线程池大同小异
31. else{
32. returnObservable.defer(newFunc0<Observable<R>>() {...}
33. }
34. }

线程隔离threadPool.getScheduler



* //隔离线程池初始化
* private staticHystrixThreadPoolinitThreadPool(HystrixThreadPool fromConstructor,HystrixThradPoolKey threadPoolKey,HystrixThreadPoolProperties.Setter threadPoolPropertiesDefaults) {
* if(fromConstructor==null) {
* // get the default implementation of HystrixThreadPool
* returnHystrixThreadPool.Factory.getInstance(threadPoolKey,threadPoolPropertiesDefaults);

6}else{

7returnfromConstructor;

* }
* }

10

11

12. staticHystrixThreadPoolgetInstance(HystrixThreadPoolKey threadPoolKey,HystrixThreadPoolProperties.Setter propertiesBuilder) {
13. // get the key to use instead of using the object itself so that if people forget to impleme nt equals/hashcode things will still work
14. String key=threadPoolKey.name();

15

16. // this should find it for all but the first time
17. HystrixThreadPool previouslyCached=threadPools.get(key);
18. if(previouslyCached!=null) {
19. returnpreviouslyCached;
20. }

21

22. // if we get here this is the first time so we need to initialize
23. synchronized(HystrixThreadPool.class) {
24. if(!threadPools.containsKey(key)) {
25. threadPools.put(key,newHystrixThreadPoolDefault(threadPoolKey,propertiesBuilder));
26. }

![图片](https://uploader.shimo.im/f/p3ndZlYUxxckWjeQ.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

27. }
28. returnthreadPools.get(key);
29. }

30

31. publicHystrixThreadPoolDefault(HystrixThreadPoolKey threadPoolKey,HystrixThreadPoolProperties.Setter propertiesDefaults) {
32. this.properties=HystrixPropertiesFactory.getThreadPoolProperties(threadPoolKey,propertiesDefaults);
33. HystrixConcurrencyStrategy concurrencyStrategy=HystrixPlugins.getInstance().getConcurrencyStrategy();
34. this.queueSize=properties.maxQueueSize().get();

35

36. this.metrics=HystrixThreadPoolMetrics.getInstance(threadPoolKey,
37. concurrencyStrategy.getThreadPool(threadPoolKey,properties),
38. properties);
39. this.threadPool=this.metrics.getThreadPool();
40. this.queue=this.threadPool.getQueue();

41

42. /* strategy: HystrixMetricsPublisherThreadPool */
43. HystrixMetricsPublisherFactory.createOrRetrievePublisherForThreadPool(threadPoolKey,this.me trics,this.properties);
44. }




命令真正的调用逻辑入口getUserExecutionObservable



* privateObservable<R>getUserExecutionObservable(final AbstractCommand<R>_cmd) {

2Observable<R>userObservable;

3

* try{
* userObservable=getExecutionObservable();

6}catch(Throwable ex) {

* // the run() method is a user provided implementation so can throw instead of using Observab e.onError
* // so we catch it here and turn it into Observable.error

9userObservable=Observable.error(ex);

10. }

11

12. returnuserObservable
13. .lift(newExecutionHookApplication(_cmd))
14. .lift(newDeprecatedOnRunHookApplication(_cmd));
15. }

16

17. finalprotectedObservable<R>getExecutionObservable() {
18. returnObservable.defer(newFunc0<Observable<R>>() {
19. @Override
20. publicObservable<R>call() {
21. try{
22. //调用命令的真正方法run()入口
23. returnObservable.just(run());
24. }catch(Throwable ex) {
25. returnObservable.error(ex);
26. }
27. }
28. }).doOnSubscribe(newAction0() {
29. @Override
30. public voidcall() {
31. // Save thread on which we get subscribed so that we can interrupt it later if needed
32. executionThread.set(Thread.currentThread());
33. }
34. });
35. }

上面方法层层调用，倒过来看，就是先创建一个Observable，然后绑定各种事件对应的处理者，如下图

![图片](https://uploader.shimo.im/f/rtvkFC81QkC8Tzhm.jpeg!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)






















断路器源码分析

Hystrix 有点类似，例如：以秒为单位来统计请求的处理情况(成功请求数量、失败请求数、超时请求数、被拒绝的请求数)，然后每次取最近10秒的数据来进行计算，如果失败率超过50%，就进行熔断，不再处理任何请求。这是Hystrix官网的一张图：

![图片](https://uploader.shimo.im/f/RJswIE196ZhOvn8w.jpeg!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)















它演示了 Hystrix 滑动窗口 策略，假定以秒为单位来统计请求处理情况，上面每个格子代表1秒，格子中的数据就是1秒内各处理结果的请求数量，格子称为 Bucket(译为桶)。

若每次的决策都以10个Bucket的数据为依据，计算10个Bucket的请求处理情况，当失败率超过50%时就熔断。

10个Bucket就是10秒，这个10秒就是一个 滑动窗口(Rolling window)。

为什么叫滑动窗口？因为在没有熔断时，每当收集好一个新的Bucket后，就会丢弃掉最旧的一个Bucket。上图中的深色的(23 5 2 0)就是被丢弃的桶。

下面是官方完整的流程图，策略是：不断收集数据，达到条件就熔断；熔断后拒绝所有请求一段时间(sleepWindow)；然后放一个请求过去，如果请求成功，则关闭熔断器，否则继续打开熔断器。

![图片](https://uploader.shimo.im/f/U6V39OQJH1CkFqA0.jpeg!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)






























相关配置

默认配置都在H ystrixC om m andProperties类中。

先看两个metrics收集的配置。

metrics.rollingStats.timeInMilliseconds

![图片](https://uploader.shimo.im/f/vHnXwOde7ss9qL8F.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

表示滑动窗口的时间(the duration of the statistical rolling window)，默认10000(10s)，也是熔断器计算的基

本单位。

metrics.rollingStats.numBuckets

![图片](https://uploader.shimo.im/f/NZN7z6R22O6o04uJ.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

滑动窗口的Bucket数量(the number of buckets the rolling statistical window is divided into)，默认10. 通过timeInMilliseconds和numBuckets可以计算出每个Bucket的时长。 metrics.rollingStats.timeInMilliseconds % metrics.rollingStats.numBuckets 必须等于 0，否则将抛异常。再看看熔断器的配置。

circuitBreaker.requestVolumeThreshold

![图片](https://uploader.shimo.im/f/Qa2C2UasJR68NMP3.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

滑动窗口触发熔断的最小请求数。如果值是20，但滑动窗口的时间内请求数只有19，那即使19个请求全部失败，也不会熔断，必须达到这个值才行，否则样本太少，没有意义。

circuitBreaker.sleepWindowInMilliseconds

![图片](https://uploader.shimo.im/f/3Bwfztrack2Se33k.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

这个和熔断器自动恢复有关，为了检测后端服务是否恢复，可以放一个请求过去试探一下。sleepWindow指的发生熔断后，必须隔sleepWindow这么长的时间，才能放请求过去试探下服务是否恢复。默认是5s

circuitBreaker.errorThresholdPercentage

![图片](https://uploader.shimo.im/f/NvHHBlx8p3znr0BS.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

错误率阈值，表示达到熔断的条件。比如默认的50%，当一个滑动窗口内，失败率达到50%时就会触发熔断。


断路器的初始化是在AbstractCommand构造器中做的初始化



* private staticHystrixCircuitBreakerinitCircuitBreaker(boolean enabled,HystrixCircuitBreakefromConstructor,HystrixCommandKey commandKey...) {
* //如果启用了熔断器

![图片](https://uploader.shimo.im/f/uwzfRpohRgzuQbnQ.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

* if(enabled) {
* //若commandKey没有对应的CircuitBreaker,则创建

5if(fromConstructor==null) {

6returnHystrixCircuitBreaker.Factory.getInstance(commandKey,groupKey,properties,metrics);7}else{

* //如果有则返回现有的
* returnfromConstructor;
10. }
11. }else{
12. return newNoOpCircuitBreaker();
13. }
14. }

再看看 HystrixCircuitBreaker.Factory.getInstance(commandKey, groupKey, properties, metrics) 如何

创建circuit-breakder？

circuitBreaker以commandKey为维度，每个commandKey都会有对应的circuitBreaker



* public staticHystrixCircuitBreakergetInstance(HystrixCommandKey key,HystrixCommandGroupKeygroup,HystrixCommandProperties properties,HystrixCommandMetrics metrics) {
* //如果有则返回现有的, key.name()即command的name作为检索条件
* HystrixCircuitBreaker previouslyCached=circuitBreakersByCommand.get(key.name());

4if(previouslyCached!=null) {

5returnpreviouslyCached;

* }
* //如果没有则创建并cache
* HystrixCircuitBreaker cbForCommand=circuitBreakersByCommand.putIfAbsent(key.name(),newHytrixCircuitBreakerImpl(key,group,properties,metrics));
* if(cbForCommand==null) {
10. returncircuitBreakersByCommand.get(key.name());
11. }else{
12. returncbForCommand;
13. }
14. }

15

16

17. //初始化断路器
18. protectedHystrixCircuitBreakerImpl(HystrixCommandKey key,HystrixCommandGroupKey commandGroup,final HystrixCommandProperties properties,HystrixCommandMetrics metrics) {
19. this.properties=properties;
20. //这是Command中的metrics对象,metrics对象也是commandKey维度的
21. this.metrics=metrics;
22. //重点:订阅事件流
23. Subscription s=subscribeToStream();
24. activeSubscription.set(s);
25. }
26. //订阅事件流,各事件以结构化数据汇入了Stream中
27. privateSubscriptionsubscribeToStream() {
28. // HealthCountsStream是重点
29. returnmetrics.getHealthCountsStream()
30. .observe()
31. //利用数据统计的结果HealthCounts,实现熔断器
32. .subscribe(newSubscriber<HealthCounts>() {

![图片](https://uploader.shimo.im/f/xtorCy2IVuSvkLMT.png!thumbnail?fileGuid=JUGbQIaRL6EnMZ5v)

33. @Override
34. public voidonCompleted() {}
35. @Override
36. public voidonError(Throwable e) {}
37. @Override
38. public voidonNext(HealthCounts hc) {
39. //检查是否达到最小请求数,默认20个;未达到的话即使请求全部失败也不会熔断
40. if(hc.getTotalRequests()<properties.circuitBreakerRequestVolumeThreshold().get()) {
41. //啥也不做
42. }else{
43. //错误百分比未达到设定的阀值
44. if(hc.getErrorPercentage()<properties.circuitBreakerErrorThresholdPercentage().get()) {
45. }else{
46. //错误率过高,进行熔断
47. if(status.compareAndSet(Status.CLOSED,Status.OPEN)) {
48. circuitOpened.set(System.currentTimeMillis());
49. }
50. }
51. }
52. }
53. });
54. }
