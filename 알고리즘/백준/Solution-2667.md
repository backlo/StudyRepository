# 백준 - Solution2667

<hr>

문제링크 : [https://www.acmicpc.net/problem/2667](https://www.acmicpc.net/problem/2667)

* Java

```java
import java.util.Arrays;
import java.util.Scanner;

public class Solution2667 {
    private static int count = 1;
    private static final int[] dx = {0, 0, 1, -1};
    private static final int[] dy = {1, -1, 0, 0};

    private static void dfs(int[][] areas, boolean[][] visited, int x, int y) {
        areas[x][y] = count;
        visited[x][y] = true;
        for (int i = 0; i < 4; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];

            if (checkLocation(nx, ny)) {
                if (areas[nx][ny] == 1 && !visited[nx][ny]) {
                    dfs(areas, visited, nx, ny);
                }
            }
        }
    }

    private static boolean checkLocation(int x, int y) {
        return x >= 0 && x <= 25 && y >= 0 && y <= 25;
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int areaLength = scanner.nextInt();
        int[][] areas = new int[areaLength + 1][areaLength + 1];
        boolean[][] visited = new boolean[areaLength + 1][areaLength + 1];

        for (int i = 0; i < areaLength; i++) {
            String str = scanner.next();
            for (int j = 0; j < areaLength; j++) {
                areas[i][j] = str.charAt(j) - '0';
            }
        }

        for (int i = 0; i < areaLength; i++) {
            for (int j = 0; j < areaLength; j++) {
                if (areas[i][j] == 1 && !visited[i][j]) {
                    dfs(areas, visited, i, j);
                    count++;
                }
            }
        }

        System.out.println(count - 1);

        int[] apartments = new int[count];
        for (int i = 0; i < areaLength; i++) {
            for (int j = 0; j < areaLength; j++) {
                if (areas[i][j] != 0) {
                    apartments[areas[i][j]]++;
                }
            }
        }

        Arrays.sort(apartments);

        for (int i = 1; i < apartments.length; i++) {
            System.out.println(apartments[i]);
        }
    }
}
```

