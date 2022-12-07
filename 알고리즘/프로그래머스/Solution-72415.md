# 프로그래머스 - 카드 짝 맞추기

<hr>


문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/72415](https://programmers.co.kr/learn/courses/30/lessons/72415)

* Java

```java
import java.util.*;

class Solution {
    private final List<String> cards = new ArrayList<>();
    private final int[] dx = {1, -1, 0, 0};
    private final int[] dy = {0, 0, 1, -1};

    public int solution(int[][] board, int r, int c) {
        int answer = Integer.MAX_VALUE;
        int cardNumber = 0;
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[i].length; j++) {
                if (board[i][j] != 0) {
                    cardNumber++;
                }
            }
        }
        int[] cardNumbers = new int[cardNumber / 2];

        for (int i = 0; i < cardNumber / 2; i++) {
            cardNumbers[i] = i + 1;
        }

        cardPermutation("", 0, cardNumbers);

        for (String card : cards) {
            String[] cardOrders = card.split(" ");

            int count = 0;
            int[] position = new int[2];
            position[0] = r;
            position[1] = c;

            int[][] copyBoard = new int[board.length][board.length];
            for (int i = 0; i < board.length; i++) {
                System.arraycopy(board[i], 0, copyBoard[i], 0, board[i].length);
            }

            for (int i = 1; i < cardOrders.length; i++) {
                int target = Integer.parseInt(cardOrders[i]);
                count += cardSearch(position, target, copyBoard);
                copyBoard[position[0]][position[1]] = 0;
                ++count;

                count += cardSearch(position, target, copyBoard);
                copyBoard[position[0]][position[1]] = 0;
                ++count;
            }

            answer = Math.min(answer, count);
        }


        return answer;
    }

    private void cardPermutation(String comb, int depth, int[] cardNumbers) {
        if (cardNumbers.length == depth) {
            cards.add(comb);
            return;
        }
        for (int i = 0; i < cardNumbers.length; i++) {
            int number = cardNumbers[i];
            if (!comb.contains(" " + number)) {
                cardPermutation(comb + " " + number, depth + 1, cardNumbers);
            }
        }
    }

    private int cardSearch(int[] position, int target, int[][] copyBoard) {
        Queue<Pair> q = new LinkedList<>();
        boolean[][] visited = new boolean[copyBoard.length][copyBoard.length];
        int x = position[0];
        int y = position[1];

        visited[x][y] = true;
        q.add(new Pair(x, y, 0));
        while (!q.isEmpty()) {
            Pair next = q.poll();
            int pX = next.x;
            int pY = next.y;
            int pMove = next.move;

            if (copyBoard[next.x][next.y] == target) {
                position[0] = next.x;
                position[1] = next.y;
                return pMove;
            }

            for (int i = 0; i < 4; i++) {
                int nx = pX + dx[i];
                int ny = pY + dy[i];

                if (nx < 0 || ny < 0 || nx > 3 || ny > 3) continue;
                if (visited[nx][ny]) continue;

                visited[nx][ny] = true;
                q.add(new Pair(nx, ny, pMove + 1));
            }

            for (int i = 0; i < 4; i++) {
                Pair res = checkRoute(pX, pY, i, copyBoard);
                int nx = res.x;
                int ny = res.y;

                if (nx == x && ny == y) continue;
                if (visited[nx][ny]) continue;

                visited[nx][ny] = true;
                q.add(new Pair(nx, ny, pMove + 1));
            }
        }

        return 0;
    }

    private Pair checkRoute(int x, int y, int direction, int[][] copyBoard) {
        x += dx[direction];
        y += dy[direction];
        while (x >= 0 && x < 4 && y >= 0 && y < 4) {
            if (copyBoard[x][y] != 0)
                return new Pair(x, y, 0);
            x += dx[direction];
            y += dy[direction];
        }
        return new Pair(x - dx[direction], y - dy[direction], 0);
    }

    static class Pair {
        int x;
        int y;
        int move;

        public Pair(int x, int y, int move) {
            this.x = x;
            this.y = y;
            this.move = move;
        }
    }
}
```

