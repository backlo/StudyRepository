# 백준 - Solution2798

<hr>

문제링크 : [https://www.acmicpc.net/problem/2798](https://www.acmicpc.net/problem/2798)

* Java

```java
import java.util.Arrays;
import java.util.List;
import java.util.Scanner;
import java.util.stream.Collectors;


public class Solution2798 {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        String[] inputs_nm = scanner.nextLine().split(" ");
        String[] inputs_numbers = scanner.nextLine().split(" ");

        int n = Integer.parseInt(inputs_nm[0]);
        int m = Integer.parseInt(inputs_nm[1]);

        List<Integer> numbers = Arrays.stream(inputs_numbers)
                .map(Integer::parseInt)
                .collect(Collectors.toList());

        int max = -1;

        for (int i = 0; i < n; i++) {
            int a = numbers.get(i);
            for (int j = 0; j < n; j++) {
                int b = numbers.get(j);
                for (int k = 0; k < n; k++) {
                    int c = numbers.get(k);
                    int sum = a + b + c;
                    if (max <= sum & sum <= m) {
                        max = sum;
                    }
                }
            }
        }

        System.out.println(max);
    }
}
```

