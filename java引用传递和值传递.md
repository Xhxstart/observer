# java引用传递和值传递

前面我们说过java中并不存在引用调用，这点是没错的，
因为java程序设计语言确实是采用了按值调用，即call by value。
也就是说方法得到的是所有参数值的一个拷贝，方法并不能修改传递给它的任何参数变量的内容。
package com.zejian.test;
/**
 * java中的按值调用
 * @author zejian
 */
public class CallByValue {
	
	private static int x=10;
	
	public static void updateValue(int value){
		value = 3 * value;
	}
	
	public static void main(String[] args) {
		System.out.println("调用前x的值："+x);
		updateValue(x);
		System.out.println("调用后x的值："+x);
	}
	
}

一个方法不能修改一个基本数据类型的参数（数值型和布尔型）。

一个方法可以修改一个引用所指向的对象状态，但这仍然是按值调用而非引用调用。

上面两种传递都进行了值拷贝的过程。
