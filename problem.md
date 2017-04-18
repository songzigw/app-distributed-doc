
### 1. Question: ssh: connect to host 192.168.188.177 port 22: Connection refused

出现这个问题的原因是ssh-server服务器没有安装或启动，我的操作系统ubuntu-16.04-desktop-amd64.iso，运行 `ps -e|grep ssh` 查看是否有sshd进程；

通过 `/etc/init.d/ssh -start` 启动server进程，如果提示ssh不存在，那么就是没安装服务，通过 `sudo apt-get install openssh-server`命令安装即可。

## 2. Question: 如何查看机器的名称

`hostname`

## 3. Question: 如何修改机器的名称

```
$ sudo vi /etc/hostname
$ sudo /etc/init.d/hostname.sh start
$ sudo reboot
```

## 4. Question: 迭代子模式

迭代子模式又叫游标模式，它可以顺序的访问一个聚集内的元素，将访问的过程封装在迭代子中，而不暴露聚集的内部表象。

它整个设计模式中属于类的行为模式。

迭代子模式的角色划分：

1. 抽象的聚集角色（Aggregate）
2. 抽象的迭代子角色（Iterator）
3. 具体的聚集角色（ConcreteAggregate）
4. 具体的迭代子角色（ConcreteIterator）
5. 客户端使用者角色（Client）

类图如下：

![iterator](https://github.com/songzigw/app-distributed-doc/blob/master/imgs/iterator.png)

``` java
public interface Aggregate {

    public Iterator createIterator();
    
}
```

``` java
public interface Iterator {

    void first();
    
    void next();
    
    boolean isDone();
    
    Object currentItem();
}
```

``` java
public class ConcreteAggregate implements Aggregate {

    private Object[] objs = {"MonkTang", "Monk", "Pigsy"};
    
    public Iterator createIterator() {
        return new ConcreteIterator(this);
    }
    
    public Object getElement(int index) {
        if (index < objs.length) {
            return objs[index];
        }
        return null;
    }
    
    public int size() {
        return objs.length;
    }
}
```

``` java
public class ConcreteIterator implements Iterator {

    private ConcreteAggregate agg;
    private int index = 0;
    private int size = 0;
    
    public ConcreteIterator(ConcreteAggregate agg) {
        this.agg = agg;
        this.size = agg.size();
        this.index = 0;
    }
    
    @Override
    public Object currentItem() {
        return agg.getElement(index);
    }

    @Override
    public void first() {
        this.index = 0;
    }

    @Override
    public boolean isDone() {
        return (index >= size);
    }

    @Override
    public void next() {
        if (index < size) {
            index++;
        }
    }

}
```

``` java
public class Client {

    private Iterator it;
    private Aggregate agg = new ConcreteAggregate();
    
    public void operation() {
        it = agg.createIterator();
        while (!it.isDone()) {
            System.out.println(it.currentItem());
            it.next();
        }
    }
    
    public static void main(String[] args) {
        Client client = new Client();
        client.operation();
    }
}
```

为什么要有迭代子模式，因为迭代子模式遵守了开闭原则（OCP），客户端委派迭代子对聚集进行遍历，当遍历算法需要改变时，客户端的代码是不需要变动的，当聚集本身需要改变时，客户端代码也无需改动。
