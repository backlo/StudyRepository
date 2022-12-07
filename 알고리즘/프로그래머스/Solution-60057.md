# 프로그래머스 - 문자열 압축

<hr>


문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/60057](https://programmers.co.kr/learn/courses/30/lessons/60057)

* Java

```java
class Solution {
    public int solution(String s) {
        int answer = s.length();

        for (int i = 1; i <= s.length() / 2; i++) {
            StringBuilder sb = new StringBuilder();
            String compareString = "";
            int count = 1;
            for (int j = 0; j < Math.ceil((double) s.length() / i); j++) {
                String sliceStr = s.substring(i * j, Math.min(i * (j + 1), s.length()));
                if (!compareString.equals(sliceStr)) {
                    if (count != 1) {
                        sb.append(count);
                        count = 1;
                    }
                    sb.append(compareString);
                    compareString = sliceStr;
                } else {
                    count++;
                }
            }
            if (count != 1) {
                sb.append(count);
            }
            sb.append(compareString);

            answer = Math.min(answer, sb.toString().length());
        }

        return answer;
    }
}
```

