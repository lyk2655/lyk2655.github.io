### `==`
public class AutoUnboxingTest {
    public static void main(String[] args) {
        Integer a = new Integer(3);
        Integer b = 3;                  // 将3自动装箱成Integer类型
        int c = 3;
        System.out.println(a == b);     // false 两个引用没有引用同一对象
        System.out.println(a == c);     // true a自动拆箱成int类型再和c比较

        Integer f1 = 100, f2 = 100, f3 = 150, f4 = 150;
        System.out.println(f1 == f2); //true
        System.out.println(f3 == f4); //true
        //我们给一个Integer对象赋一个int值的时候，会调用Integer类的静态方法valueOf
        //public static Integer valueOf(int i) {
		// if (i >= IntegerCache.low && i <= IntegerCache.high)
		// return IntegerCache.cache[i + (-IntegerCache.low)];
		// return new Integer(i);
		// }
        //简单的说，如果整型字面量的值在-128到127之间，那么不会new新的Integer对象，而是直接引用常量池中的Integer对象，
        //所以上面的面试题中f1==f2的结果是true，而f3==f4的结果是false。
    }
}