# 프로그래머스 - 로또의 최고 순위와 최저 순위

<hr>

문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/77484](https://programmers.co.kr/learn/courses/30/lessons/77484)

* Java

```java
import java.util.Arrays;

public class Solution77484 {

    public int[] solution(int[] lottos, int[] win_nums) {
        int[] answer = new int[2];

        int count = 0;
        int unknownCount = 0;

        for (int lotto : lottos) {
            if (lotto == 0) {
                ++unknownCount;
                continue;
            }
            for (int win_num : win_nums) {
                if (lotto == win_num) {
                    ++count;
                }
            }
        }

        answer[0] = Rank.findRank(count + unknownCount);
        answer[1] = Rank.findRank(count);

        return answer;
    }

    enum Rank {
        FIRST_RANK(1, 6),
        SECOND_RANK(2, 5),
        THIRD_RANK(3, 4),
        FOURTH_RANK(4, 3),
        FIFTH_RANK(5, 2);

        private final int rank;
        private final int checkNumber;

        Rank(int rank, int checkNumber) {
            this.rank = rank;
            this.checkNumber = checkNumber;
        }

        private static int findRank(int checkNumber) {
            for (Rank rank : values()) {
                if (checkNumber == rank.checkNumber) {
                    return rank.rank;
                }
            }
            return 6;
        }
    }
}

```

