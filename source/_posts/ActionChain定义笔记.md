---
title: 'ActionChain定义笔记.md'
date: 2019-04-03 14:31:47
---
<a name="889248f2"></a>
#### Action贯穿在库存交易操作，提供链式操作方便迭代扩展，将流程从代码实现逻辑中抽出提炼为标准，使用时通过指定action及顺序实现相关业务
```java
public class ActionChain<T extends ActionContext> implements Appendable<T> {
    //存储具体操作链表
		private final List<IAction<T>> chain = new ArrayList<>();
		//存储拦截器链表
    private final List<IInterceptor<T>> interceptors = new ArrayList<>();
		//添加具体操作
    @Override
    public Appendable<T> appendAction(Class<? extends IAction<T>> action) {
        chain.add(ActionBeanFactory.getActionInstance(action));
        return this;
    }
		//添加拦截器
    @Override
    public Appendable<T> appendInterceptor(IInterceptor<T> interceptor) {
        interceptors.add(interceptor);
        return this;
    }
  	//执行逻辑
    public ActionResult execute(T context) {
        IAction<T> currentAction = null;

        ActionResult actionResult = ActionResult.success();

        while (!context.isIgnored() && !context.isIdempotent() && actionResult.isSuccess()
                && (currentAction = nextAction(currentAction)) != null) {
            try {
                intercept(context, currentAction, actionResult, Stage.BEFORE, null);
                currentAction.execute(context);
                intercept(context, currentAction, actionResult, Stage.AFTER, null);
            } catch (Exception ex) {
                intercept(context, currentAction, actionResult, Stage.EXCEPTION, ex);
            } finally {
                intercept(context, currentAction, actionResult, Stage.FINAL, null);
            }
        }
        actionResult.forwardContextStatus(context.isIgnored(), context.isIdempotent());
        return actionResult;
    }
  
		private void intercept(T param, IAction<T> handler, ActionResult result, Stage loc, Throwable ex) {}
  	
  	public enum Stage {
        BEFORE,
        AFTER,
        FINAL,
        EXCEPTION
    }
}
```
<a name="8e1ff949"></a>
#### 在使用时主要通过实现IAction<T>接口中execute()方法的action实现类、实现IInterceptor<T>接口中beforeHandle()、afterHandle()、errorHandle()和finalHandle()方法的interceptor实现类和继承ActionContext的具体场景上下文完成逻辑编写实现
