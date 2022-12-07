# 프로그래머스 - 신규 아이디 추천

<hr>


문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/72412](https://programmers.co.kr/learn/courses/30/lessons/72412)

* Java

```java
import java.util.*;

public class Solution {

    public int[] solution(String[] info, String[] query) {
        Map<String, List<Integer>> applicants = new HashMap<>();
        for (String in : info) {
            String[] infoList = in.split(" ");
            int score = Integer.parseInt(infoList[4]);
            combination(infoList, score, applicants);
        }

        for (Map.Entry<String, List<Integer>> entry : applicants.entrySet()){
            entry.getValue().sort(null);
        }

        int[] answer = new int[query.length];
        for(int i = 0; i < query.length; i++) {
            String[] splits = query[i].replaceAll("-", "").replaceAll(" and ", "").split(" ");
            String key = splits[0];
            int score = Integer.parseInt(splits[1]);
            List<Integer> list = applicants.getOrDefault(key, new ArrayList<>());
            int start = 0;
            int end = list.size();
            while (start < end) {
                int mid = (start + end) / 2;
                if(list.get(mid) < score) {
                    start = mid + 1;
                }else {
                    end = mid;
                }
            }
            answer[i] = list.size() - start;
        }
        return answer;
    }

    private void combination(String[] infoList, int score, Map<String, List<Integer>> applicants) {
        for (int i = 0; i < (1 << 4); i++) {
            StringBuilder key = new StringBuilder();
            for (int j = 0; j < 4; j++) {
                if ((i & (1 << j)) > 0) key.append(infoList[j]);
            }
            applicants.computeIfAbsent(key.toString(), s -> new ArrayList<>()).add(score);
        }
    }
}
```