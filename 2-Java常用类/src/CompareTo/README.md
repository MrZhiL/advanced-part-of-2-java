## 9. Java比较器

Java对象中，正常情况下，只能进行比较：== 或 !=。不能使用 > 或 < 的。

但是在开发场景中，我们需要对多个对象进行排序，言外之意，就需要比较对象的大小。

如何实现？使用两个接口中的任何一个：Comparable 或 Comparator

### 1. Comparable接口的使用举例：（自然排序）

1. 像String、包装类等实现了Comparable接口，重写了compareTo(obj)方法，给出了比较两个对象的实现方式
2. 像String、包装类重写compareTo(obj)方法以后，默认为进行了从小到大的排序
3. 重写compareTo(obj)的规则：
   - 如果当前对象this小于形参对象obj，则返回正整数
   - 如果当前对象this大于形参对象obj，则返回负整数
   - 如果当前对象this等于形参对象obj，则返回零。
4. 对于自定义类来说，如果需要排序，我们可以让自定义类实现Compareble类，重写compareTo()方法并指明如何排序

```java
public void test01() {
    // String类的sort排序
    String[] arr = {"KKK", "AAA", "CC", "II", "ZZ", "DD", "GG"};

    Arrays.sort(arr); // [AAA, CC, DD, GG, II, KKK, ZZ]

    System.out.println(Arrays.toString(arr));
}

public void test02() {
    Goods[] goods = new Goods[5];
    goods[0] = new Goods("xiaomi Phone", 1999);
    goods[1] = new Goods("Huawei Phone", 2999);
    goods[2] = new Goods("Lenovo Phone", 1999);
    goods[3] = new Goods("Oppo Phone", 2400);
    goods[4] = new Goods("vivo Phone", 2599);

    for (int i = 0; i < goods.length; ++i) {
        System.out.println(goods[i].getName() + ", " + goods[i].getPrice());
    }
    System.out.println(Arrays.toString(goods));

    Arrays.sort(goods);
    System.out.println("After sort");
    for (int i = 0; i < goods.length; ++i) {
        System.out.println(goods[i].getName() + ", " + goods[i].getPrice());
    }
    System.out.println(Arrays.deepToString(goods));
}
```



### 2. 方式二：定制排序：java.util.Comparator

- 当元素类型没有实现 `java.lang.Comparable`接口而又不方便修改代码，或者实现了 `java.lang.Comparable`接口的排序规则不适合当前的操作，那么可以考虑使用Comparator的对象来排序，强行对多个对象进行整体排序的比较
- 重写compare(Object o1, Object o2)方法，比较o1和o2的大小：如果方法返回正整数，则表示o1大于o2；如果返回0，表示相等；如果返回负整数，表示o1小于o2。
- 可以将Comparator传递给sort方法（如Collections.sort或Arrays.sort），从而允许在排序顺序上实现精度控制。
- 还可以使用Comparator来控制某些数据结构（如有序set或有序映射）的顺序，或者为那些没有自然顺序的对象collection提供排序。

```java
package CompareTo;

import org.junit.Test;

import java.util.Arrays;
import java.util.Comparator;

/**
 * @ClassName: ComparatorTest
 * @Description: Java - Comparator接口比较
 * @author: zhilx (zhilx1997@sina.com)
 * @version: v1.0
 * @data: 2022/2/19 10:58
 * @node: 方式二：定制排序：java.util.Comparator
 *        1  - 当元素类型没有实现 `java.lang.Comparable`接口而又不方便修改代码，
 *          或者实现了 `java.lang.Comparable`接口的排序规则不适合当前的操作，
 *          那么可以考虑使用Comparator的对象来排序，强行对多个对象进行整体排序的比较
 *        2  - 重写compare(Object o1, Object o2)方法，比较o1和o2的大小：
 *          如果方法返回正整数，则表示o1大于o2；
 *          如果返回0，表示相等；
 *          如果返回负整数，表示o1小于o2。
 *        3  - 可以将Comparator传递给sort方法（如Collections.sort或Arrays.sort），从而允许在排序顺序上实现精度控制。
 *        4  - 还可以使用Comparator来控制某些数据结构（如有序set或有序映射）的顺序，或者为那些没有自然顺序的对象collection提供排序。
 *
 *
 */
public class ComparatorTest {
    @Test
    public void test01() {
        // String类的sort排序,指定为从大到小
        String[] arr = {"KKK", "AAA", "CC", "II", "ZZ", "DD", "GG"};

        Arrays.sort(arr, new Comparator() {

            @Override
            public int compare(Object o1, Object o2) {
                if (o1 instanceof  String && o2 instanceof String) {
                    String str1 = (String) o1;
                    String str2 = (String) o2;

                    return str2.compareTo(str1);
                }

                throw new RuntimeException("数据输入类型不匹配！");
            }
        }); // [ZZ, KKK, II, GG, DD, CC, AAA]

        System.out.println(Arrays.toString(arr));
    }

    @Test
    public void test2() {
        Goods[] goods = new Goods[6];
        goods[0] = new Goods("xiaomi Phone", 1999);
        goods[1] = new Goods("Huawei Phone", 2999);
        goods[2] = new Goods("Lenovo Phone", 1999);
        goods[3] = new Goods("Oppo Phone", 2400);
        goods[4] = new Goods("vivo Phone", 2599);
        goods[5] = new Goods("xiaomi Phone", 3299);

        // 指明商品的排序方式：名称从小到大,价格从高到低
        Arrays.sort(goods, new Comparator() {

            @Override
            public int compare(Object o1, Object o2) {
                if (o1 instanceof Goods && o2 instanceof Goods) {
                    Goods goods1 = (Goods) o1;
                    Goods goods2 = (Goods) o2;

                    if (goods1.getName().equals(goods2.getName())) {
                        return Double.compare(goods2.getPrice(), goods1.getPrice());
                    } else {
                        return goods1.getName().compareTo(goods2.getName());
                    }
                }
                throw new RuntimeException("数据类型输入不匹配!");
            }
        });

        System.out.println(Arrays.toString(goods));
    }
}

```



### 3. 对比：

- Comparator接口属于临时性的比较，只能使用一次。

- Comparable接口的方式一旦固定，保证Comparable接口实现类的对象在任何位置都可以比较大小。

