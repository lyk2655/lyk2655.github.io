### 题目描述：
一个系统的若干模块之间存在互相依赖的关系，如果A模块调用了B模块的接口，那么成为A依赖B，记为A->B
如果A->B，B->A，那么称A和B模块都存在循环依赖。
如果A->B，B->C，C->D，D->B，那么BCD存在循环依赖，A不存在循环依赖，以此类推
先输入若干模块之间的关系，判断某模块是否存在循环依赖。模块ID范围为0--0xffffffff
调用者保证相同的关系不重复输入

#### 输入描述：

模块间关系，注意最后一行没有“，”结尾

#### 输出描述：
模块是否循环依赖，注意最后一行没有“，”结尾


#### 输入用例：
{0x00, 0x01},
{0x02, 0x03},
{0x03, 0x04}

#### 输出：
{0x00, false},
{0x01, false},
{0x02, false},
{0x03, false},
{0x04, false}

[参考思路](https://blog.csdn.net/u011831771/article/details/77284676)

```java
package test;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

public class module {
	public static void main(String[] args) {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		Map<String, String> arr = new HashMap<String, String>();
		Map<String, Integer> out = new HashMap<String, Integer>();
		while (true) {
			try {
				String str = br.readLine();
				int i = 3;
				StringBuffer a = new StringBuffer("");
				StringBuffer b = new StringBuffer("");
				for (; str.charAt(i) != ','; i++) {
					a.append(str.charAt(i));
				}
				i += 4;
				for (; str.charAt(i) != '}'; i++) {
					b.append(str.charAt(i));
				}
				//System.out.println();
				//System.out.println(a + "     " + b);
				arr.put(a.toString(), b.toString());
				out.put(a.toString(), 0);
				out.put(b.toString(), 0);
				//System.out.println(i+" i-len "+str.length());
				if (i == str.length()-1) {
					break;
				}

			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		//for (String k : arr.keySet()) {
		//	System.out.println("{0x" + k + ", 0x" + arr.get(k) + "}");
		//}

		for (String k : arr.keySet()) {
			Set<String> s = new HashSet<String>();
			s.add(k);
			String v = arr.get(k);
			s.add(v);
			String vv = arr.get(v);
			while (vv != null) {
				if (s.contains(vv)) {
					Iterator<String> it = s.iterator();
					while (it.hasNext()) {
						String sss = it.next();
						out.put(sss, 1);
					}
					break;
				}
				vv = arr.get(vv);
			}

		}
		Iterator<String> it = out.keySet().iterator();
		while (it.hasNext()) {
			String k = it.next();
			System.out.print("{0x" + k + ", ");
			if (out.get(k) == 1) {
				System.out.print("ture}");
			} else {
				System.out.print("false}");
			}
			if(it.hasNext()) {
				System.out.println(",");
			} else {
				System.out.println();
			}
		}
	}
}

```
