# Java 三种集合的遍历方式

### List集合

四种：普通for循环、增强for循环（foreach）、迭代器（iterator）、forEach

```java
package demo05;
 
import java.util.ArrayList;
import java.util.Collections;
import java.util.Iterator;
import java.util.List;
 
public class Test01 {
    public static void main(String[] args) {
        List<Integer> list=new ArrayList<>();
        Collections.addAll(list,10,90,20,80,30,70,40,60,50);
        System.out.println(list);//[10, 90, 20, 80, 30, 70, 40, 60, 50]
 
        //普通for循环遍历
        for (int i = 0; i < list.size(); i++) {
            System.out.print(list.get(i)+" ");
        }
        System.out.println();
 
        //foreach遍历，又叫增强for循环
        for (Integer num : list) {
            System.out.print(num+" ");
        }
        System.out.println();
 
        //迭代器遍历
        Iterator<Integer> iter = list.iterator();
        while (iter.hasNext()){
            System.out.print(iter.next()+" ");
        }
        System.out.println();
 
        //forEach遍历
        list.forEach(num-> System.out.print(num+" "));
    }
}
```

### Set集合

三种：增强for循环（foreach）、迭代器（iterator）、forEach

```java
package demo05;
 
import java.util.Collections;
import java.util.Iterator;
import java.util.LinkedHashSet;
import java.util.Set;
 
public class Test02 {
    public static void main(String[] args) {
        Set<Integer> set=new LinkedHashSet<>();
        Collections.addAll(set,10,90,20,80,30,70,40,60,50);
        System.out.println(set);
 
        //foreach遍历
        for (Integer num : set) {
            System.out.print(num+" ");
        }
        System.out.println();
 
        //迭代器遍历
        Iterator<Integer> iter = set.iterator();
        while (iter.hasNext()){
            System.out.print(iter.next()+" ");
        }
        System.out.println();
 
        //forEach遍历
        set.forEach(num-> System.out.print(num+" "));
    }
}
```

## Map集合

第一种 `keySet`：`map`集合调用keySet方法返回的是所有的键值组成的set集合，所有再调用`keySet`方法后，再调用map集合的get`（Object key）`方法，通过键获取值，这样就可以遍历了。

第二种 `entrySet`：调用该方法返回的是此映射中包含的映射关系的Set集合，之后再调用`getKey()`和`getValue()`方法即可遍历。
```java
package demo05;
 
import java.util.LinkedHashMap;
import java.util.Map;
import java.util.Set;
 
public class Test03 {
    public static void main(String[] args) {
        Map<Integer,String> map=new LinkedHashMap<>();
        map.put(1,"a");
        map.put(9,"c");
        map.put(2,"e");
        map.put(8,"g");
        map.put(3,"h");
        map.put(7,"f");
        map.put(6,"d");
        map.put(5,"b");
        System.out.println(map);
 
        //获取keySet后，foreach遍历
        Set<Integer> keySet = map.keySet();
        for (Integer key : keySet) {
            System.out.print(key+"="+map.get(key)+"\t");
        }
        System.out.println();
 
        //获取entrySet后，foreach遍历
        Set<Map.Entry<Integer, String>> entries = map.entrySet();
        for (Map.Entry<Integer, String> entry : entries) {
            System.out.print(entry.getKey()+"="+entry.getValue()+"\t");
        }
        System.out.println();
    }
}
```

