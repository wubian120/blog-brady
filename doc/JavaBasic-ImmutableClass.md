# Java 不可变类

>immutable object 不可变类是指这个类的实例一旦创建完成后，就不能改变其成员变量值。
>如JDK内部自带的很多不可变类：Interger、Long和String等。
> ** 不可变类，指的是 该类的实例不可变，而非指向该实例的引用的不可变**

## 优点

- 线程安全
- 当一个对象是不可变的，那么需要拷贝这个对象的内容时，就不用复制它的本身而只是复制它的地址，复制地址（通常一个指针的大小只需要很小的内存空间，具有非常高的效率。同时，对于引用该对象的其他变量也不会造成影响。
- 此外，不变性保证了hashCode 的唯一性，因此可以放心地进行缓存而不必每次重新计算新的哈希码。而哈希码被频繁地使用, 比如在hashMap 等容器中。将hashCode 缓存可以提高以不变类实例为key的容器的性能。



## 如何实现

1. 所有成员都是private final;
2. 不提供改变成员的方法，setter ;
3. 确保所有方法不会被重载， 使用final class 强不可变类，或 所有方法加上final;
4. 如果一个类的成员，不是基本类型primitive type或不可变类， 则在成员初始化和 getter方法时，深度拷贝（复制类的实例而非引用）;
5. 如果有必要，重写hashCode equals， 应保证在equals相等时，hashCode也相等。



## 通过反射修改String 对象的值

//用反射获取 修改string 值；
``` 
    public static void main(String[] args){
        String s= "Hello World";
        System.out.println(" s: "+s);
        
        try {
            Field value = String.class.getDeclaredField("value");
            value.setAccessible(true);
            char[] vs = (char[]) value.get(s);

            vs[5] = 'F';
            
            System.out.println(" s after: "+ s);

        }catch (Exception e){
            e.printStackTrace();
        }
    }


```
如果需要设计一个不可变类，尤其要注意其引用类型Field，如果其引用类型Field的类是可变的，就必须采取必要的措施来保护该Field所引用的对象不会被修改，这样才能创建真正的不可变类。

```
class Name{
    private String first;
    private String last;

    public Name(){}

    public Name(String f, String l){
        first = f;
        last  = l;
    }

    public void setFirst(String first) {this.first = first;}
    public String getFirst() {return first;}

    public String getLast() {return last;}
    public void setLast(String last) {this.last = last;}

    @Override
    public String toString() {
        return "Frist: "+first + " last: " + last;
    }
}

class Person{
    private final Name name;

    public Person(Name name){
        this.name = new Name(name.getFirst(),name.getLast());
    }

    public Name getName() {
        return new Name(name.getFirst(),name.getLast());
    }

    @Override
    public String toString() {
        return getClass().getName() + "@[name="+this.getName() +"]";
    }
}

public class ImmutableTestTwo {
    public static void main(String[] args){

        Name n1 = new Name("悟空", "孙");
        Person p1 = new Person(n1);
        System.out.println(p1.getName().getFirst());
        n1.setFirst("八戒");
        System.out.println(p1.getName().getFirst());
    }
}

```
或者做如下修改：
将Name类的setter方法删除掉，这样Name类是不可变的类，Person类也是不可变的类。



## 参考资料



[从final的作用剖析String被设计成不可变类的深层原因](http://www.cnblogs.com/zfyouxi/p/3804654.html?utm_source=tuicool&utm_medium=referral)

[创建正真的Java不可变类](http://www.cnblogs.com/bluepoint2009/p/create-immutable-class.html?utm_source=tuicool&utm_medium=referral)

