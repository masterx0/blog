---
title: '库存寻源业务逻辑梳理.md'
date: 2019-04-03 14:31:47
---
#### 寻源业务场景
 - 商品详情页查看当前区域商品可售数量
	 - 直降活动库存大于实际可售库存，详情页查询要以实际可售库存为准
	 - 直降活动库存小于实际可售库存，详情页查询要以直降活动库存为准
	 - 区域限购无可售库存
 - 商品购物车
	 - 在商品已经加入购物车后，由于库存数量改变导致不满足购物车条件要提示库存不足
	 - 在商品加入购物车后，切换配送地址要及时反馈不可售情况
 - 单sku下单
	 - 预售活动库存小于仓库实际库存，按照仓库优先级扣减库存，在单sku下单数量高于实际库存时提示无法购买
	 - 但仓库数量不满足下单sku数量时拆单规则要符合仓库优先级设置
 - 一个商家多个sku下单
	 - 普通场景下尽量在仓库优先级设置下要以同一仓库发货为准
	 - 组合商品数量在任何仓库都不满足同一仓库出库的前提下提示不可售
 - 买赠场景
 	 - 买商品A赠商品B在寻源时配置优先级
 	 - 买A赠C & 买B赠A默认遵循赠品优先于主商品的逻辑  
#### 寻源核心逻辑
![寻源流程图](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/124729/1547016084904-46fe3085-8264-4e26-b396-4c0e1c00eb54.png)
![包结构](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/124729/1551771097372-46431d8f-61fa-4add-b12d-9bdb978450b1.png) 
*v5.0.18.1.RELEASE*寻源基于工作流的方式进行
1. **sourcing prehandler**: 寻源参数预处理，主要分为以下四个步骤
- io.terminus.parana.inventory.server.sourcing.prehandle.support.DivisionValidator 实现仓库配送范围参数校验，针对不配送区域直接返回
- io.terminus.parana.inventory.server.sourcing.prehandle.support.SourcingRuleLoad 主要是寻源规则的加载并处理，如配置是否必须同仓出货等
- io.terminus.parana.inventory.server.sourcing.prehandle.support.SourcingQuantityPreHandler 控制NPE，将寻源的商品数量<=0的设置为1
- io.terminus.parana.inventory.server.sourcing.prehandle.support.WarehousePrehandler 仓库相关预处理，剔除不符合要求的一部分

2. **assemble bucket**：组装组合商品，进行商品及数量的合并,  默认提供io.terminus.parana.inventory.server.sourcing.bucket.support.DefaultBucketAssemble支持相关逻辑，内部经由group()、combineSameSku()和handleOutingRule()处理后将SourcingBuckets存入payload中

3. **bucket sourcing**：寻源的主要逻辑
 - bucket initial process: 初步过滤，默认提供BucketWarehouseHandler中处理针对入参的仓库集和寻源规则，区域限售针对仓库的过滤等
 - data load：寻源的数据加载，加载Inventory->加载渠道库存->处理仓库维度独占问题->加载warehouse
 - warehouse filter：二次仓库过滤，合并仓库，去掉warehouse中的inventory
 - check quantity: 计算最大可贩卖库存，默认提供DefaultCheckQuantity实现主要逻辑
 - satisfied warehouse selector: 针对库存不满足需求的仓库进行过滤，默认提供DefaultWarehouseSelector和EntityQuantityWarehouseSelector实现主要逻辑
 - bucket special process: 出库特殊处理，对于不能被workflow接下来逻辑影响的策略要提前进行处理，针对海信的有货即发，必须同仓以上两个寻源规则，海信不需要考虑最小拆仓原则，优先在bucket处理掉

4. **combine warehouse**: 针对不同的业务合并仓库,默认提供io.terminus.parana.inventory.server.sourcing.combination.support.DefaultWarehouseReCombination支持相关逻辑，将满足条件的仓库合并成CombinedWarehouseExt列表存储在payload中

5. **sort warehouse**: 过滤仓库，默认提供DefaultWarehouseSort内部通过Comparator的选择实现主要逻辑
 - MatchedComparator：根据仓库命中数排序，命中数越高排名越前
 - IndexComparator：根据仓库index排序，index值越小排名越前
 - QuantityComparator：根据仓库可卖sku数量排序，数量越多排名越前

6. **choose best warehouse**：选择最优仓库，默认提供DefaultWarehouseChoose实现主要逻辑

上述所有操作都是可配置化的，在业务层面建立SourcingConfig注入到Spring中，利用策略模式选择合适的处理策略，配合责任链模式和命令模式实现具体功能。

#### 寻源多线程相关
1.多线程执行parallelExecution用于经过preHandler和AssembleBucket处理之后的核心部分**bucketSourcing**中
2.接口定义如下：
``` java
public interface ParallelExecution {
    void start(ParallelExecutionAction action, ParallelExecutionContext context, Collection<? extends ParallelExecutionParam> collection);
}
```
内部通过使用CountDownLatch实现多线程并发，实际调用：
``` java
public interface ParallelServiceTemplate<T> {
    Response<T> executeParallel(ParallelServiceCallback<T> callback);

}
```
在ParallelServiceTemplateImpl中调用定义的注解@ParallelExecute标注的ParallelAction.executeAction()在切面ParallelExecuteUtility执行实际多线程业务
``` java
 @Around(value = "@annotation(parallelExecute)")
    public void execute(ProceedingJoinPoint joinPoint, ParallelExecute parallelExecute) {
	  ...
		threadPoolExecutor.execute(() -> {
                try {
                    joinPoint.proceed(parameters);
                } catch (Throwable throwable) {
                    callback.setEx(throwable);
                } finally {
                    callback.getCountDownLatch().countDown();
                }
            });
	  ...
	}
```
#### 寻源服务接口*io.terminus.parana.inventory.server.sourcing.InventorySourcingService*
``` java
//首先在阅读源码过程中，该接口实现类有几点我觉得是在技术实现上是可以改进的
//1.下面的代码中SourcingResultExecutor在逻辑中单独注入运行，为什么不添加到WorkflowBuilder
//后放入Workflow中执行，单独提出来的原因在源码中没看出来，觉得可能是原始代码是不同人接手完成的
try {
            log.info("sourcing begin ,param:{}", JSON.toJSON(sourcingParam));
            WorkFlow<InventorySourcingByDimension, Payload> workFlow = workflowBuilder.build(sourcingParam);
            payload = createPayLoad(sourcingParam, tenantId);
            payload.setWorkFlow(workFlow);
            workFlow.exec(sourcingParam, payload);
        } catch (SourcingException e) {
            log.error("inventory.sourcing.fail", e);
        } catch (Exception e) {
            log.error("inventory.sourcing.fail", e);
        }
        sourcingResultExecutor.execute(sourcingParam, payload);
        InventorySourcingByDimension sourcingResult = payload.getSourcingResult();
//2.捕捉异常SourcingException和Exception报错信息相同的话合并下是不是更好
//3.寻源service没有使用接口定义，直接在service类中写了Implement实现逻辑
``` 
#### 工作流中主要参数*InventorySourcingByDimension*、*InventoryBucket*和*Payload*
 - **InventorySourcingByDimension** 被库存服务接口唯一参数inventorySourcing内数组包装，包含寻源dto列表sourcingInventories、维度dimensionId、维度类型dimensionType、区域divisionIds、仓库信息warehouses、寻源选项sourcingOption等字段
 - **Payload** 包含业务id、工作流workflow、**InventorySourcingByDimension**、bucketList、寻源各个节点配置sourcingConfig、排序过的寻源规则优先级sortedSourcingRules、寻源返回值sourcingResult等字段
 - **InventoryBucket** 库存捆绑信息是寻源的基础，来源于**Payload**中的bucketList计算赋值的寻源库存集合内，包含仓库库存是否满足satisfied、满足数量要求的仓库列表warehouseResult、该bucket使用的寻源规则列表sourcingRules、出库规则outingRule等字段
#### 工作流实现类和寻源工作流实现类*DefaultWorkflowBuilder&DefaultSourcing *
 - DefaultWorkflowBuilder定义了PreSourcingHandleExecutor->BucketAssembleExecutor->**SourcingExecutor**->WarehouseCombinationExecutor->WarehouseSortExecutor->WarehouseChooseExecutor工作流程
 - DefaultSourcing则将**SourcingExecutor**细分为BucketBeforeHandlerExecutor->DataloadExecutor->DefaultWarehouseFilterExecutor->DefaultCheckQuantityExecutor->WarehouseSelectExecutor->BucketAfterHandlerExecutor工作流程
#### 寻源执行实现类*io.terminus.parana.inventory.server.sourcing.AbstractSourcingExecutor*
 - 实现了Executor接口的execute()方法，内部通过Payload.getSourcingConfig().getActionConfig()获取负载参数集中Action枚举值与寻源扩展处理类的映射关系，再迭代执行定义的抽象方doExecute(bean,param,payload)实现主要逻辑。
 - 定义了protected List<T> extendClass(Payload payload）获取extend扩展类列表
 - 义了protected List<String> extendClassName(Payload payload）获取extend扩展类名称
 - 定义了抽象方法protected abstract void doExecute(T bean,P param,Payload payload）供实现类编写执行逻辑
 - 定义了抽象方法protected abstract SourcingConfig.ActionEnum action(）获取Action枚举值
 - 定义了抽象方法public abstract Class<T> ExtendClass(）与Action枚举值对应的Action接口

  

