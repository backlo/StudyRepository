# 백준 - Solution2231

<hr>

문제링크 : [https://www.acmicpc.net/problem/2231](https://www.acmicpc.net/problem/2231)

* Java

```java
import java.util.Scanner;

public class Solution2231 {

    private static int solution(int number) {
        int result = 0;

        for (int i = 1; i < number; i++) {
            int tmp = i;
            int sum = tmp;
            int length =  (int)(Math.log10(i)) + 1;

           sum += sumCipher(i, length);

            if (sum == number) {
                result = i;
                break;
            }
        }

        return result;
    }

    private static int sumCipher(int i, int size) {
        int result = 0;
        int tmp = i;
        for (int j = size; j > 0; j--) {
            result += tmp % 10;
            tmp -= (tmp % 10);
            tmp /= 10;
        }

        return result;
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        int number = scanner.nextInt();
        System.out.println(solution(number));
    }
}
```

