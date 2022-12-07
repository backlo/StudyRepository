# 프로그래머스 - 경주로 건설

<hr>

문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/67259](https://programmers.co.kr/learn/courses/30/lessons/67259)

* Java

```java
import java.util.LinkedList;
import java.util.Queue;

public class Solution67259 {
    private static final String[] DIRECTION = {"RIGHT", "LEFT", "UP", "DOWN"};
    private final int[] dx = {1, -1, 0, 0};
    private final int[] dy = {0, 0, 1, -1};
    private boolean[][] visited;
    private int answer = Integer.MAX_VALUE;

    public int solution(int[][] board) {
        int n = board.length;
        visited = new boolean[n][n];

        bfs(0, 0, "", 0, board, n);

        return answer;
    }

    private void bfs(int x, int y, String direction, int cost, int[][] board, int boardSize) {
        Queue<Road> q = new LinkedList<>();
        q.add(new Road(x, y, direction, cost));
        visited[x][y] = true;

        while (!q.isEmpty()) {
            Road road = q.remove();
            int queueX = road.x;
            int queueY = road.y;
            String queueDirection = road.direction;
            int queueCost = road.cost;

            if (queueX == boardSize - 1 && queueY == boardSize - 1) {
                answer = Math.min(answer, queueCost);
            }

            for (int i = 0; i < 4; i++) {
                int nextX = queueX + dx[i];
                int nextY = queueY + dy[i];
                String nextDirection = DIRECTION[i];
                int nextCost = queueCost;

                if (nextX < 0 || nextY < 0 || nextX >= boardSize || nextY >= boardSize || board[nextX][nextY] == 1) {
                    continue;
                }

                if (queueDirection.equals("") || queueDirection.equals(nextDirection)) {
                    nextCost += 100;
                } else {
                    nextCost += 600;
                }

                if (!visited[nextX][nextY] || board[nextX][nextY] >= nextCost) {
                    visited[nextX][nextY] = true;
                    board[nextX][nextY] = nextCost;
                    q.add(new Road(nextX, nextY, nextDirection, nextCost));
                }
            }
        }
    }

    class Road {
        private final int x;
        private final int y;
        private final String direction;
        private final int cost;

        public Road(int x, int y, String direction, int cost) {
            this.x = x;
            this.y = y;
            this.direction = direction;
            this.cost = cost;
        }
    }

}
```

