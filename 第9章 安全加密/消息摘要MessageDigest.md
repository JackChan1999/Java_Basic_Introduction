## Java安全加密专题文章索引

1. [Java安全加密：对称加密](http://blog.csdn.net/axi295309066/article/details/52491077)
2. [Java安全加密：非对称加密](http://blog.csdn.net/axi295309066/article/details/52494640)
3. [Java安全加密：消息摘要Message Digest](http://blog.csdn.net/axi295309066/article/details/52494725)
4. [Java安全加密：数字签名和数字证书](http://blog.csdn.net/axi295309066/article/details/52494832)
5. [Java安全加密：Https编程](http://blog.csdn.net/axi295309066/article/details/52494902)


# 1. 常见算法
MD5（已经被攻破）、SHA（sha1，sha256，sha512）、CRC、SM系列等

# 2. 使用场景

- 对用户密码进行md5 加密后保存到数据库里
- 软件下载站使用消息摘要计算文件指纹，防止被篡改
- 数字签名（后面知识点）
- 百度云，360网盘等云盘的妙传功能用的就是sha1值
- Eclipse和Android Studio开发工具根据sha1值来判断v4，v7包是否冲突
- 据说银行的密码使用的就是MD5加密（因为MD5具有不可逆性）
- 病毒查杀，把每个病毒文件或apk进行MD5后得到一个特征码，拿着特征码去跟病毒数据库对比，特征码一致说明该文件是病毒
- Git版本控制也使用到了sha1
- 加盐: 就是在密码的前面加入一些特殊字符%^&&%^&

例如软件下载站数据指纹：http://dev.mysql.com/downloads/installer/

![数字摘要](http://img.blog.csdn.net/20160910135955384)

## 2.1 Git计算校验

Git 用以计算校验和的机制叫做 SHA-1 散列（hash，哈希）。这是一个由 40 个十六进制字符（0-9 和 a-f）组成字符串，基于 Git 中文件的内容或目录结构计算出来。SHA-1 哈希看起来是这样：

24b9da6552252987aa493b52f8696cd6d3b00373

Git 中使用这种哈希值的情况很多，你将经常看到这种哈希值。实际上，Git 数据库中保存的信息都是以文件内容的哈希值来索引，而不是文件名。


# 3. 使用步骤

```java
//常用算法：MD5、SHA、CRC
MessageDigest digest = MessageDigest.getInstance("MD5");
byte[] result = digest.digest(content.getBytes());
//消息摘要的结果一般都是转换成16 进制字符串形式展示
String hex = Hex.encode(result);
//MD5 结果为16 字节（128 个比特位）、转换为16 进制表示后长度是32 个字符
//SHA 结果为20 字节（160 个比特位）、转换为16 进制表示后长度是40 个字符
System.out.println(hex);
```
消息摘要后的结果是固定长度，无论你的数据有多大，哪怕是只有一个字节或者是一个G 的文件，摘要后的结果都是固定长度。

经常听到有人问这样的问题，MD5 摘要后结果到底是多少位？

有的人说是16 位，有的说是128 位，有的说是32 位。到底是多长，这个时候我们就要明白，16 位指的是字节位数，128 位指的是比特位，32 位指的结果转换成16 进制展示的字符位数。

# 4. 数字摘要原理

MD5的全称为Message-Digest Algorithm 5，即消息摘要算法第五版，是当前计算机领域用于确保信息传输完整一致而广泛使用的散列算法之一。MD5算法的功能是将数据运算变为另一固定长度值，是散列算法的基础原理。MD5的前身有MD2、MD3和MD4。

- 用每个byte去和11111111（0xff）做与运算并且得到的是int类型的值

   byte & 11111111得到有效的低8位

- 把int 类型转成 16进制并返回String类型
- 不满八个二进制位就补全

```java
//获取MD5算法对象
MessageDigest digest = MessageDigest.getInstance("MD5");
digest.update(key.getBytes());
// 对字符串加密，返回加密后的字节数组
byte[] bytes = digest.digest(key.getBytes());
StringBuilder sb = new StringBuilder();
for (int i = 0; i < bytes.length; i++) {
  	// 获取有效的低8位
    String hex = Integer.toHexString(bytes[i]&0xff);
    if (hex.length() == 1){
        sb.append("0");
    }
    sb.append(hex);
}
String hexstring = sb.toString();
```

```java
public class MD5Utils {
    
    public static String encode(String pwd) {
        // MessageDigest 消息摘要
        try {
            MessageDigest digester = MessageDigest.getInstance("MD5");
            // 加密，将要加密的字符串转换成字节数组
            byte[] digest = digester.digest(pwd.getBytes());

            StringBuffer buffer = new StringBuffer();
            // 0000-0101
            // 1111-1111
            // 0000-0101
            for (byte b : digest) {
                // 0xff 表示低8位
                int number = b & 0xff;
                // int 32位，一个int是四个字节，一个字节8位
                // 任何一个值& 0等于0
                // & 1 = 1
                String hexString = Integer.toHexString(number);
                if (hexString.length() == 1) {
                    buffer.append("0" + hexString);
                } else {
                    buffer.append(hexString);
                }
            }
            System.out.println("密码长度---" + buffer.toString().length());
            System.out.println("密码---" + buffer.toString());
            return buffer.toString();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return "";
    }
}
```
## 4.1 字节数组与16进制字符串转换工具

```java
/**
 * 字节数组与16进制字符串转换工具
 */
public class Hex {

	private static final char[] HEX_CHAR = { '0', '1', '2', '3', '4', '5', '6',
			'7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f' };

	/**
	 * 字节数组转换成16进制字符串
	 * 
	 * @param bytes
	 * @return
	 */
	public static String encode(byte[] bytes) {
		if (bytes == null || bytes.length == 0) {
			return null;
		}

		StringBuffer sb = new StringBuffer(bytes.length * 2);
		// 27对应的十六进制为1b,对应的二进制是00011011
		// 取高位和低位：00011011-》0001,1011-》1,b
		for (int i = 0; i < bytes.length; ++i) {
			// 取高位：跟0xf0做与运算后再右移4位
			int high = (bytes[i] & 0xf0) >> 4;// 0xf0: 11110000
			// 取低位：跟0x0f做与运算
			int low = bytes[i] & 0x0f;// 0x0f: 00001111
			// 字符映射
			sb.append(HEX_CHAR[high]).append(HEX_CHAR[low]);
		}
		return sb.toString();
	}

	/**
	 * 16进制字符串转换为字节数组
	 * 
	 * @param hex 16进制字符  
	 * @return
	 */
	public static byte[] decode(String hex) {
		if (hex == null || hex.length() == 0) {
			return null;
		}

		// 16进制转byte，长度减半，"1b"-->27
		int len = hex.length() / 2;
		byte[] result = new byte[len];
		String highStr = null;
		String lowStr = null;
		int high = 0;
		int low = 0;
		for (int i = 0; i < len; i++) {
			// 高位值
			highStr = hex.substring(i * 2, i * 2 + 1);// "1b"的高位为"1"
			high = Integer.parseInt(highStr, 16);// 高位转为10进制
			// 低位值
			lowStr = hex.substring(i * 2 + 1, i * 2 + 2);// "1b"的低位为"b"
			low = Integer.parseInt(lowStr, 16);// 低位转为10进制
			// 合计值
			result[i] = (byte) ((high << 4) + low);// 相当于:(高位*16) + 低位
		}
		return result;
	}

}
```

# 5. MD5解密网站

http://www.cmd5.com/

为了提高MD5 加密的安全性，减少密文被反向解密的可能性，应尽量将密码的长度设置的长一些，另外也可以将密码进行多次加密，这样可以保护用户的隐私安全