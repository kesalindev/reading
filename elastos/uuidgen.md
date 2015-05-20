# 生成UUID

## 前言
---
在 Elastos 系统开发过程中，我们常常需要获得 uuid 作为标识某个类的 InterfaceID。

例如 View，我们在 View.h 中声明：
``` cpp
extern "C" const InterfaceID EIID_View;
```
并在 View.cpp 中提供定义：
``` cpp
extern "C" const InterfaceID EIID_View =
        { 0x47439045, 0x473d, 0x4d3c, { 0x87, 0x6b, 0x42, 0x42, 0x0, 0xc8, 0x9e, 0x19 } };
```

下面提供两种生成 uuid 的办法。

## 使用系统命令<code>uuidgen</code>
---
在 terminal 中输入:<code>uuidgen</code>，会得到一串数字，将这串数字替入 cpp 中的 InterfaceID 的定义中即可。
``` shell
$ uuidgen
f64e77ea-4ae0-4cee-b348-c94b35184cf8
```

## 使用 java 代码生成
---
由于 uuidgen 提供的 uuid 并不能很好地满足 InterfaceID 的格式要求，因此，我写了一段 java 代码以提供更好的格式支持。可点击如下链接在线编译运行：[http://ideone.com/MdDOTs](http://ideone.com/MdDOTs)

具体的 java 源码如下：
``` java
import java.util.HashMap;
import java.util.UUID;

class UUIDGen {

    // for header file
    // extern "C" const InterfaceID EIID_ActivityClientRecord;
    //
    // for cpp file
    // CAR_INTERFACE_IMPL_WITH_CPP_CAST(ActivityClientRecord, IInterface, ActivityClientRecord)

    void generate()
    {
        final int genCount = 1;

        HashMap<String, String> uuidMap = new HashMap<String, String>();
        for (int i = 0; i < genCount; ++i) {
            uuidMap.put("EIID_XXXXXX", UUID.randomUUID().toString());
        }

        for(String key: uuidMap.keySet()){
            String uuid = uuidMap.get(key);

            if (uuidMap.size() == 1) {
                System.out.println("\nextern \"C\" const InterfaceID " + key + ";\n");
            }

            System.out.println("// " + uuid);
            System.out.println("extern \"C\" const InterfaceID " + key + " =");

            int idx1 = uuid.indexOf("-");
            System.out.print("    { 0x");
            System.out.print(uuid.substring(0, idx1));

            int idx2 = uuid.indexOf("-", idx1 + 1);
            System.out.print(", 0x");
            System.out.print(uuid.substring(idx1 + 1, idx2));

            idx1 = uuid.indexOf("-", idx2 + 1);
            System.out.print(", 0x");
            System.out.print(uuid.substring(idx2 + 1, idx1));

            idx2 = uuid.indexOf("-", idx1 + 1);
            String subStr = uuid.substring(idx1 + 1, idx2);
            System.out.print(", { 0x");
            System.out.print(subStr.substring(0, 2));
            System.out.print(", 0x");
            System.out.print(subStr.substring(2));

            idx2 = uuid.lastIndexOf("-");
            subStr = uuid.substring(idx2 + 1);
            System.out.print(", 0x");
            System.out.print(subStr.substring(0, 2));
            System.out.print(", 0x");
            System.out.print(subStr.substring(2, 4));
            System.out.print(", 0x");
            System.out.print(subStr.substring(4, 6));
            System.out.print(", 0x");
            System.out.print(subStr.substring(6, 8));
            System.out.print(", 0x");
            System.out.print(subStr.substring(8, 10));
            System.out.print(", 0x");
            System.out.print(subStr.substring(10));

            System.out.println(" } };");
            System.out.println("");
        }
    }

    /**
     * @param args
     */
    public static void main(String[] args) {
        UUIDGen test = new UUIDGen();
        test.generate();
    }
}
```
