# 反射

Java的反射机制为Java带来了动态性，可以在运行时去识别在编译时不知道的类/对象的信息，，并且能够调用相应的方法并修改属性值。

```java
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class Test {

	private String name;
	public int age;
	protected boolean sex;

	public static void main(String[] args)
			throws InstantiationException, IllegalAccessException, ClassNotFoundException {
		Test test = new Test();
		test.reflection(test);



	}

	public void reflection(Object object){
		Class clazz = object.getClass();
		System.out.println(clazz);

		System.out.println("=========================");

		// 获取类的名称
		String className = clazz.getName();
		// 获取类中定义的方法
		Method[] methods = clazz.getMethods();
		// 获取类中定义的成员
		Field[] fields = clazz.getFields();

		System.out.println(className);

		System.out.println("=========================");

		for (Method method : methods) {
			System.out.println(method.getName());
		}
		System.out.println("=========================");

		for (Field field : fields) {
			System.out.println(field.getName());
		}
		// 只能打印Public方法
		/*
		 * public Field[] getFields() throws SecurityException {
		 * checkMemberAccess(Member.PUBLIC, Reflection.getCallerClass(), true);
		 * return copyFields(privateGetPublicFields(null)); }
		 */

		System.out.println("=========================");

		// 动态执行方法
		try {
			Method method = clazz.getDeclaredMethod("add01", int.class, int.class);
			System.out.println(method.invoke(this, 1, 1));

		} catch (NoSuchMethodException e) {
			e.printStackTrace();
		} catch (SecurityException e) {
			e.printStackTrace();
		} catch (IllegalArgumentException e) {
			e.printStackTrace();
		} catch (InvocationTargetException e) {
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			e.printStackTrace();
		}

		System.out.println("=========================");
		//动态操作属性
		Field field;
		try {
			field = clazz.getDeclaredField("name");
			field.set(this, "测试");
			System.out.println(field.get(this));
		} catch (NoSuchFieldException | SecurityException e) {
			e.printStackTrace();
		} catch (IllegalArgumentException e) {
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			e.printStackTrace();
		}

	}

	public int add01(int a, int b) {
		return a + b;
	}

	public void add02() {

	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	public boolean isSex() {
		return sex;
	}

	public void setSex(boolean sex) {
		this.sex = sex;
	}

}
```