# 백준 - Solution1436

<hr>

문제링크 : [https://www.acmicpc.net/problem/1436](https://www.acmicpc.net/problem/1436)

* Java

```java
import java.util.Scanner;

public class Solution1436 {

    private static String solution(int num) {
        String result = "";
        String endNum = "666";
        int checkNum = 666;
        int count = 0;

        while(true) {
            String strValue = String.valueOf(checkNum);
            
            if (strValue.contains(endNum)) {
                result = strValue;
                ++count;
            }
            
            if(count == num) {
                break;
            }
            ++checkNum;
        }
        return result;
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int num = scanner.nextInt();
        System.out.println(solution(num));
    }
}
```

