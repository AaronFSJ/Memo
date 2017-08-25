# 非UTF8字符过滤 #
UTF8是边长字符集，单个字符占用1~4个字符。在Mysql中，UTF8字符集只能保存3个字节的字符，如果想要保存4个字节的字符，需要是用utf8mb4字符集。但是在一些情况下，我们只能使用3个字符集时，需要多超过3个的字符进行过滤。另外庆幸的是汉子是三个字节的。
```java
public static String filterOffUtf8Mb4(String text) throws UnsupportedEncodingException {
		byte[] bytes = text.getBytes("utf-8");
		ByteBuffer buffer = ByteBuffer.allocate(bytes.length);
		int i = 0;
		while (i < bytes.length) {
			short b = bytes[i];
			if (b > 0) {
				buffer.put(bytes[i++]);
				continue;
			}
			b += 256;
			if ((b ^ 0xC0) >> 4 == 0) {
				buffer.put(bytes, i, 2);
				i += 2;
			} else if ((b ^ 0xE0) >> 4 == 0) {
				buffer.put(bytes, i, 3);
				i += 3;
			} else if ((b ^ 0xF0) >> 4 == 0) {
				i += 4;
			}
		}
		buffer.flip();
		return new String(buffer.array(), "utf-8");
	}
```
