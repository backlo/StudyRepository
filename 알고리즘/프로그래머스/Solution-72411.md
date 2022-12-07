# 프로그래머스 - 메뉴 리뉴얼

<hr>


문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/72411](https://programmers.co.kr/learn/courses/30/lessons/72411)

* Java 

```java
import java.util.*;

public class Solution {

    public String[] solution(String[] orders, int[] course) {
        Map<String, Integer> map = new HashMap<>();

        for (String order : orders) {
            for (int courseNum : course) {
                char[] array = order.toCharArray();
                boolean[] visited = new boolean[array.length];

                combination(array, visited, 0, array.length, courseNum, map);
            }
        }

        int[] bestCounts = new int[course.length];
        List<String> results = new ArrayList<>();

        for (int i = 0; i < course.length; i++) {
            bestCounts[i] = getBestCombination(course[i], map);

            for (Map.Entry<String, Integer> entry : map.entrySet()) {
                String key = entry.getKey();
                int value = entry.getValue();
                
                if (key.length() == course[i] && value == bestCounts[i] && value != 1) {
                    results.add(key);
                }
            }
        }
        
        Collections.sort(results);
        
        return results.toArray(new String[0]);
    }

    private int getBestCombination(int course, Map<String, Integer> map) {
        int best = 0;
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            String key = entry.getKey();
            int value = entry.getValue();
            if (key.length() == course) {
                if (best < value) {
                    best = value;
                }
            }
        }
        
        return best;
    }

    private void combination(char[] array, boolean[] visited, int start, int length, int courseNum, Map<String, Integer> map) {
        if (courseNum == 0) {
            String result = toString(array, visited, length);
            if (map.containsKey(result)) {
                map.put(result, map.get(result) + 1);
            } else {
                map.put(result, 1);
            }
            return;
        }

        for (int i = start; i < length; i++) {
            visited[i] = true;
            combination(array, visited, i + 1, length, courseNum - 1, map);
            visited[i] = false;
        }
    }

    private String toString(char[] array, boolean[] visited, int length) {
        StringBuilder sb = new StringBuilder();

        Arrays.sort(array);

        for (int i = 0; i < length; i++) {
            if (visited[i]) {
                sb.append(array[i]);
            }
        }

        return sb.toString();
    }
}
```

