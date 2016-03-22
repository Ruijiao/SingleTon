# SingleTon
## 单例模式(懒汉式)的最佳写法
### 网上很多例子是这么写的
<pre>
<code>
  public class Singleton {
  	private static Singleton single;
  	private Singleton(){}
  	public static Singleton getAndFix(){
  		if(single == null){
  			single = new Singleton();
  		}
  		return single;
  	}
  }
</code>
</pre>
### 这种写法线程不安全，改进一下，加一个同步锁：
<pre>
<code>
  public class Singleton {
  	private static Singleton single;
  	private Singleton(){}
  	public static synchronized Singleton getAndFix(){
  		if(single == null){
  			single = new Singleton();
  		}
  		return single;
  	}
  }
</code>
</pre>
### 网上这样的代码更多，可以很好的工作，但是缺点是效率低。
### 实际上，早在JDK1.5就引入volatile关键字，所以又有了一种更好的双重校验锁写法：
<pre>
<code>
  public class Singleton {
  	private volatile static Singleton single;
  	private Singleton(){}
  	public static Singleton getAndFix(){
  		if(single == null){
  			synchronized (Singleton.class) {
  				if(single == null){
  					single = new Singleton();
  				}
  			}
  		}
  		return single;
	  }
  }
</code>
</pre>
### 这才是最佳写法！！！
### 解释新加的 volatile
volatile的作用是： 作为指令关键字，确保本条指令不会因编译器的优化而省略，且要求每次直接读值.
简单地说就是防止编译器对代码进行优化.比如如下程序：
<pre>
<code>
  XBYTE[2]=0x55;
  XBYTE[2]=0x56;
  XBYTE[2]=0x57;
  XBYTE[2]=0x58;
</code>
</pre>
对外部硬件而言，上述四条语句分别表示不同的操作，会产生四种不同的动作，但是编译器却会对上述四条语句进行优化，认为只有XBYTE[2]=0x58（即忽略前三条语句，只产生一条机器代码）。如果键入volatile，则编译器会逐一的进行编译并产生相应的机器代码（产生四条代码）.

以上参考：

1） http://mp.weixin.qq.com/s?__biz=MzA4MjU5NTY0NA==&mid=404388098&idx=1&sn=8bbbba7692dca68cdda2212dec4d86c0&scene=1&srcid=0322sTNZ4FASs9K89bFuNCBE#wechat_redirect

2） http://baike.baidu.com/link?url=uuY0bUwlJJfRZVDSb0Dfpyjfel8HG55hTZIfDnu1JpdP7ttejsSKamV5qit5VWkXcJudD0qOxwTc812ixxK1fa
