# Synchronized 的各种用法

1. Synchronized Method

   ```java
   synchronized public void syncMethod() {
   …
   }
   ```

   此种synchronized 用法锁定的物件为Method 所属的物件Instance，只要物件被new 出超过一个以上的Instance，就有可能保护不到Method 内程式。

2. Synchronized Static Method

   ```java
   synchronized static public void syncMethod() {
   …
   }
   ```

   此种synchronized 用法锁定的物件为Method 所属的物件的Class，不管被new 出几个的Instance，都能够保证同一个时间只会有一个Thread 在执行此Method。

3. Synchronized(this)

   ```java
   public void syncMethod() {
   synchronized(this) {
   …
   }
   }
   ```

   此种synchronized 用法与synchronized method 用法一样，都是锁定Method 所属的物件本身。

4. Synchronized(SomeObject)

   ```java
   public void syncMethod() {
   synchronized(SomeObject) {
   …
   }
   }
   ```

   此种synchronized 用法锁定的是SomeObject，如果SomeObject 是两个不同Instance，那synchronized 区块内就有可能被同时执行。

   如果每一个Synchronized 的SomeObject 都是同一个Instance (或者SomeObject 本身就是Static)，就可以保证区块内同时间只会被一个Thread 执行。

   当使用Synchronized (SomeObject) 时，SomeObject 本身处于被Lock 状态，但此时SomeObject 内的值是可以被更改的，Lock 只是同步化的状态，跟物件本身的资料无关，不代表不能更改资料。