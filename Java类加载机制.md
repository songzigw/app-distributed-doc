### Java类加载器级别

1. 启动类加载器 ( Bootstrap Loader )

2. 扩展类加载器 ( Extended Loader )

3. 系统类加载器 ( AppClass Loader )

4. 自定义类加载器

### 不同级别的加载器加载不同的类

1. 启动类加载器加载java核心类库
2. ...

### 类加载器的特定

1. 双亲委派加载机制；
2. 运行一个程序时，总是由系统类加载器(AppClass Loader)开始加载；
3. 在加载类时，每个类加载器会将加载任务上交给父类加载器，如果父类找不到，再自己去加载；
4. 启动类加载器(Bootstrap Loader)是最顶级的类加载器，其父加载器为null。

### 打印出类加载器

```
	public static void main(String[] args) {
		String s = new String("123");
		Class<?> c = s.getClass();
		ClassLoader loader = c.getClassLoader();
		outputClassLoader(loader);
	}
	
	public static void outputClassLoader(ClassLoader loader) {
		System.out.println(loader);
		if (loader != null) {
			outputClassLoader(loader.getParent());
		}
	}
```

### 类加载的三种方式

1. 命令行启动应用时候由JVM初始化加载
2. 通过Class.forName()方法动态加载
3. 通过ClassLoader.loadClass()方法动态加载

```
	public static void main(String[] args) throws ClassNotFoundException {
		ClassLoader loader = ClassLoader.getSystemClassLoader();
		System.out.println(loader);
		// 使用ClassLoader.loadClass()来加载类，不会执行初始化块
		loader.loadClass("com.Hello");
		// 使用Class.forName()来加载类，默认会执行初始化块
		Class.forName("com.Hello");
		// 使用Class.forName()来加载类，并指定ClassLoader初始化时不执行静态块
		Class.forName("com.Hello", false, loader);
	}
```


