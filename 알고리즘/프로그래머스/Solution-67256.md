# 프로그래머스 - 키패트 누르기

<hr>

문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/67256](https://programmers.co.kr/learn/courses/30/lessons/67256)

* Java

```java
public class Solution67256 {

    public String solution(int[] numbers, String hand) {
        StringBuilder answer = new StringBuilder();
        int leftFingerX = 0;
        int leftFingerY = 3;
        int rightFingerX = 2;
        int rightFingerY = 3;

        for (int number : numbers) {
            if (number == 1 || number == 4 || number == 7) {
                answer.append("L");
                leftFingerX = 0;
                leftFingerY = number / 3;
                continue;
            }

            if (number == 3 || number == 6 || number == 9) {
                answer.append("R");
                rightFingerX = 2;
                rightFingerY = number / 3 - 1;
                continue;
            }

            int nextX = 1;
            int nextY = number == 0 ? 3 : number / 3;

            int leftFingerLocation = calculateDistance(leftFingerX, leftFingerY, nextX, nextY);
            int rightFingerLocation = calculateDistance(rightFingerX, rightFingerY, nextX,  nextY);

            if (leftFingerLocation == rightFingerLocation)  {
                if (hand.equals("right")) {
                    answer.append("R");
                    rightFingerX = nextX;
                    rightFingerY = nextY;
                } else {
                    answer.append("L");
                    leftFingerX = nextX;
                    leftFingerY = nextY;
                }
                continue;
            }

            if (leftFingerLocation < rightFingerLocation) {
                answer.append("L");
                leftFingerX = nextX;
                leftFingerY = nextY;
                continue;
            }

            answer.append("R");
            rightFingerX = nextX;
            rightFingerY = nextY;
        }

        return answer.toString();
    }

    public int calculateDistance(int currentX, int currentY, int nextX, int nextY) {
        int resultX = Math.abs(currentX - nextX);
        int resultY = Math.abs(currentY - nextY);

        return resultX + resultY;
    }
}
```

