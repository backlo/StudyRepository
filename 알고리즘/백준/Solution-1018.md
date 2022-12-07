# 백준 - Solution1018

<hr>

문제링크 : [https://www.acmicpc.net/problem/1018](https://www.acmicpc.net/problem/1018)

* Java

```java
import java.util.Scanner;

public class Solution1018 {

    private static int solution(String[][] boards) {
        int minResult = Integer.MAX_VALUE;

        for (int i = 0; i < boards.length - 7; i++) {
            for (int j = 0; j < boards[i].length - 7; j++) {
                String[][] tmpBlackBoard = copyStringArr(boards, i, j);
                String[][] tmpWhiteBoard = copyStringArr(boards, i, j);
                int countBlack = 0;
                int countWhite = 0;
                if (tmpBlackBoard[0][0].equals("W")) {
                    countBlack++;
                }

                tmpBlackBoard[0][0] = "B";
                countBlack = checkFixBoardCount(tmpBlackBoard, countBlack);

                if (tmpWhiteBoard[0][0].equals("B")) {
                    countWhite++;
                }

                tmpWhiteBoard[0][0] = "W";
                countWhite = checkFixBoardCount(tmpWhiteBoard, countWhite);
                int count = countBlack > countWhite ? countWhite : countBlack;
                minResult = minResult > count ? count : minResult;
            }
        }

        return minResult;
    }

    private static String[][] copyStringArr(String[][] boards, int i, int j) {
        String[][] copyStringArr = new String[8][8];

        for (int k = 0; k < copyStringArr.length; k++) {
            System.arraycopy(boards[i + k], j, copyStringArr[k], 0, copyStringArr[k].length);
        }

        return copyStringArr;
    }

    private static int checkFixBoardCount(String[][] boards, int count) {
        for (int i = 0; i < boards.length; i++) {
            for (int j = 0; j < boards[i].length; j++) {
                if (i == 0 && j == 0) {
                    continue;
                }
                if (i == 0) {
                    if (boards[i][j].equals(boards[i][j - 1])) {
                        String tmp = boards[i][j].equals("B") ? "W" : "B";
                        boards[i][j] = tmp;
                        count++;
                    }
                    continue;
                }
                if (j == 0) {
                    if (boards[i][j].equals(boards[i - 1][j])) {
                        String tmp = boards[i][j].equals("B") ? "W" : "B";
                        boards[i][j] = tmp;
                        count++;
                    }
                    continue;
                }
                if (boards[i][j].equals(boards[i][j - 1]) && boards[i][j].equals(boards[i - 1][j])) {
                    String tmp = boards[i][j].equals("B") ? "W" : "B";
                    boards[i][j] = tmp;
                    count++;
                }
            }
        }
        return count;
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        String[] inputBoardSize = scanner.nextLine().split(" ");
        int height = Integer.parseInt(inputBoardSize[0]);
        int width = Integer.parseInt(inputBoardSize[1]);
        String[][] boards = new String[height][width];

        for (int i = 0; i < height; i++) {
            String[] color = scanner.nextLine().split("");
            for (int j = 0; j < width; j++) {
                boards[i][j] = color[j];
            }
        }

        System.out.println(solution(boards));
    }
}
```

