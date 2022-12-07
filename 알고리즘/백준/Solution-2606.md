# 백준 - Solution2606

<hr>

문제링크 : [https://www.acmicpc.net/problem/2606](https://www.acmicpc.net/problem/2606)

* Java

```java
import java.util.Scanner;

public class Solution2606 {

    private static int count = 0;

    private static void dfs(int[][] connections, boolean[] flag, int warm) {
        if (flag[warm]) {
            return;
        }

        flag[warm] = true;
        count++;

        for (int i = 0; i < connections[warm].length; i++) {
            if (connections[warm][i] == 1 && !flag[i]) {
                dfs(connections, flag, i);
            }
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int computerNumber = scanner.nextInt();
        int connectionNumber = scanner.nextInt();
        int[][] connections = new int[computerNumber + 1][computerNumber + 1];
        boolean[] flag = new boolean[computerNumber  + 1];

        for (int index = 0; index < connectionNumber; index++) {
            int connectionInfo1 = scanner.nextInt();
            int connectionInfo2 = scanner.nextInt();
            connections[connectionInfo1][connectionInfo2] = 1;
            connections[connectionInfo2][connectionInfo1] = 1;
        }

        dfs(connections, flag, 1);
        System.out.println(count - 1);
    }
}
```

