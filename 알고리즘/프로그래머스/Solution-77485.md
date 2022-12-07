# 프로그래머스 - 행렬 테두리 회전하기

<hr>

문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/77485](https://programmers.co.kr/learn/courses/30/lessons/77485)

* Java

```java
import java.util.Arrays;

public class Solution {
    private int[][] maps;

    public int[] solution(int rows, int columns, int[][] queries) {
        int[] answer = new int[queries.length];
        maps = new int[rows + 1][columns + 1];
        int count = 1;

        for (int i = 1; i < maps.length; i++) {
            for (int j = 1; j < maps[i].length; j++) {
                maps[i][j] = count++;
            }
        }

        for (int i = 0; i < queries.length; i++) {
            answer[i] = rotation(queries[i][0], queries[i][1], queries[i][2], queries[i][3]);
        }

        return answer;
    }

    private int rotation(int startX, int startY, int endX, int endY) {
        int min = Integer.MAX_VALUE;
        int next;
        int before;

        next = maps[startX][endY];
        for (int i = endY; i > startY; i--) {
            maps[startX][i] = maps[startX][i - 1];
        }

        before = next;
        next = maps[endX][endY];
        for (int i = endX; i > startX + 1 ; i--) {
            maps[i][endY] = maps[i - 1][endY];
        }
        maps[startX + 1][endY] = before;

        before = next;
        next = maps[endX][startY];
        for (int i = startY; i < endY - 1; i++) {
            maps[endX][i] = maps[endX][i + 1];
        }
        maps[endX][endY - 1] = before;

        for (int i = startX; i < endX - 1; i++) {
            maps[i][startY] = maps[i + 1][startY];
        }
        maps[endX - 1][startY] = next;

        for (int i = startX; i < endX + 1; i++) {
            min = Math.min(min, maps[i][startY]);
            min = Math.min(min, maps[i][endY]);
        }

        for (int i = startY; i < endY + 1; i++) {
            min = Math.min(min, maps[startX][i]);
            min = Math.min(min, maps[endX][i]);
        }

        return min;
    }
}

```

