# observer

com.mode.observer

/**
 *
 */
package com.mode.observer;

/**
 * @author kigak-ka
 *
 */
public class Test {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO 自動生成されたメソッド・スタブ
		 WechatServer server = new WechatServer();

	        Observera userZhang = new User("ZhangSan");
	        Observera userLi = new User("LiSi");
	        Observera userWang = new User("WangWu");

	        server.registerObserver(userZhang);
	        server.registerObserver(userLi);
	        server.registerObserver(userWang);
	        //server.setInfomation("PHP是世界上最好用的语言！");
	        server.setInfomation("JAVA是世界上最好用的语言！");
	        System.out.println("----------------------------------------------");
	       server.removeObserver(userZhang);
	       server.setInfomation("C是世界上最好用的语言！");
	}

}



package com.mode.observer;

/**
 * 抽象观察者
 * 定义了一个update()方法，当被观察者调用notifyObservers()方法时，观察者的update()方法会被回调。
 * @author kigak-ka
 *
 */
public interface Observera {

 public void update(String message);

}

/**
 *
 */
package com.mode.observer;


/**
 * 抽象被观察者接口
 * 声明了添加、删除、通知观察者方法
 * @author kigak-ka
 *
 */
public interface Observerable {


//	抽象被观察者角色：也就是一个抽象主题，它把所有对观察者对象的引用保存在一个集合中，每个主题都可以有任意数量的观察者。抽象主题提供一个接口，可以增加和删除观察者角色。一般用一个抽象类和接口来实现。
//	抽象观察者角色：为所有的具体观察者定义一个接口，在得到主题通知时更新自己。
//	具体被观察者角色：也就是一个具体的主题，在集体主题的内部状态改变时，所有登记过的观察者发出通知。
//	具体观察者角色：实现抽象观察者角色所需要的更新接口，一边使本身的状态与制图的状态相协调。
	public void registerObserver(Observera o);

	public void removeObserver(Observera o);

	public void notifyObserver();
}

/**
 *
 */
package com.mode.observer;

/**
 * @author kigak-ka
 *
 */
public class User implements Observera {


	private String name;

	private String message;


	//构造函数
    public User(String name) {
        this.name = name;
    }
	/* (非 Javadoc)
	 * @see com.mode.observer.Observera#update(java.lang.String)
	 */
	@Override
	public void update(String message) {
		// TODO 自動生成されたメソッド・スタブ
		this.message = message;
		read();
	}

	public void read() {
		System.out.println(name + "收到推送消息：" + message);
	}
}


/**
 *
 */
package com.mode.observer;

import java.util.ArrayList;
import java.util.List;

/**
 * 被观察者，也就是微信公众号服务
 * 实现了Observerable接口，对Observerable接口的三个方法进行了具体实现
 * @author kigak-ka
 *
 */
public class WechatServer implements Observerable {

	//注意到这个List集合的泛型参数为Observer接口，设计原则：面向接口编程而不是面向实现编程
	private List<Observera> list;

	private String message;

	public WechatServer() {
		// TODO 自動生成されたコンストラクター・スタブ
		list = new ArrayList<Observera>();
	}
	/* (非 Javadoc)
	 * @see com.mode.observer.Observerable#registerObserver(java.util.Observer)
	 */
	@Override
	public void registerObserver(Observera o) {
		// TODO 自動生成されたメソッド・スタブ
		list.add(o);
	}

	/* (非 Javadoc)
	 * @see com.mode.observer.Observerable#removeObserver(java.util.Observer)
	 */
	@Override
	public void removeObserver(Observera o) {
		// TODO 自動生成されたメソッド・スタブ
		if(!list.isEmpty()){
			list.remove(o);
		}

	}

	//遍历 有更新的话通知每一个观察者也去更新
	/* (非 Javadoc)
	 * 遍历 有更新的话通知每一个观察者也去更新
	 * @see com.mode.observer.Observerable#notifyObserver()
	 */
	@Override
	public void notifyObserver() {
		// TODO 自動生成されたメソッド・スタブ
		list.forEach(x->x.update(message));
	}

	public void setInfomation(String s) {
		this.message = s;
		System.out.println("微信服务更新：" + s);
		//消息更新，通知所有观察者
		notifyObserver();
	}
}
