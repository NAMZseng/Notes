# MD5算法

## 介绍

MD5（Message-Digest Algorithm 5，消息摘要算法 5），由 MD2、MD3、MD4 演变过来的，是一种哈希算法。

注：虽然用哈希值反推原字符串理论上是不可能的，但是可以用穷举法破解。如可以事先穷举出一个字典，从字典中直接用哈希值反查原字符串。

## 特点

-   任意长度的数据，算出的 MD5 值长度都是固定的（16或32位）
-   同一数据经过MD5计算，得到的值始终是相同的
-   对原数据进行任何改动，哪怕只修改1个字节，所得到的 MD5 值都有很大区别
-   已知原数据和其 MD5 值，想找到一个具有相同 MD5 值的数据（即伪造数据）是非常困难的

## 应用场景

-   校验数据是否篡改

    如，对一个文件，使用MD5算法，生成对应的fingerprint(指纹)，并记录在案。在传播这个文件时，别人如果修改了文件中的任何内容，那么再对这个文件重新进行MD5计算时，得到的结果就与之前记录的fingerprint(指纹)冲突，从而判断该文件被篡改。

-   密码保存

    如，对于用户的登录密码，使用MD5计算后再保存到数据库中，这样即使数据库被盗取，用户密码也不易被破解。

## JAVA代码实现

```java
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

   /**
     * 使用MD5算法加密字符串
     * @param string 待加密字符串，如用户登录密码
     * @param slat 盐值
     * @return MD5算法加密后对应的16进制字符串，长度为32
     */
    public static String md5(String string, String slat) {
        MessageDigest md5 = null;
        try {
            md5 = MessageDigest.getInstance("MD5");

            // 加盐值的目的是为加大MD5破解的难度，提高安全性。
            byte[] bytes = md5.digest((string + slat).getBytes());

            String result = "";

            // 将bytes数组中的每个数据转成对应16进制的字符串，方便显示
            // 注： 若直接使用new String(byte[] b)，则得到的字符串会存在乱码
            for (byte b : bytes) {
                // 当byte数据向上转型为int型时，java会默认保持高位不变。
                // 所有对于负数，转型后高24位就会全补1，造成结果错误。所以要通过& 0xff运算来将高24位置0。
                String temp = Integer.toHexString(b & 0xff);

                // 保证每个byte数据都转成两位字符串，使得最后对应的16进制字符串为长度为32
                if (temp.length() == 1) {
                    temp = "0" + temp;
                }
                result += temp;
            }
            return result;
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        }
        return "";
    }
```



## 参考文章

-   [Android 数据加密之 MD5 加密 - 掘金 (juejin.cn)](https://juejin.cn/post/6844903444810055687)
-   [(15条消息) java的md5算法中，为什么要将每个字节都&0xff?_yulongkuke的博客-CSDN博客](https://blog.csdn.net/yulongkuke/article/details/46607127)