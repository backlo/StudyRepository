# 프로그래머스 - 보석 쇼핑

<hr>

문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/67258](https://programmers.co.kr/learn/courses/30/lessons/67258)

* Java

```java
import java.util.*;

public class Solution67258 {
    private static final Set<String> gemType = new HashSet<>();
    private static final Queue<String> gemsToBuy = new LinkedList<>();
    private static final Map<String, Integer> gemsToBuyCount = new HashMap<>();
    private static int startIndex = 0;

    private static int answerStartIndex = 0;
    private static int answerSize = Integer.MAX_VALUE;

    public int[] solution(String[] gems) {
        countGemType(gems);
        shopGems(gems);
        return new int[]{answerStartIndex + 1, answerStartIndex + answerSize};
    }

    private void countGemType(String[] gems) {
        gemType.addAll(Arrays.asList(gems));
    }

    private void shopGems(String[] gems) {
        for (String gem : gems) {
            addGemsToBuy(gem);
            removeDuplicateStartGem();
            updateAnswer();
        }
    }

    private void addGemsToBuy(String gem) {
        gemsToBuy.add(gem);
        updateGemsToBuyCount(gem);
    }

    private void updateGemsToBuyCount(String gem) {
        if (gemsToBuyCount.containsKey(gem))
            gemsToBuyCount.put(gem, gemsToBuyCount.get(gem) + 1);
        else
            gemsToBuyCount.put(gem, 1);
    }

    private void removeDuplicateStartGem() {
        while (true) {
            String startGem = gemsToBuy.peek();
            int startGemCount = gemsToBuyCount.get(startGem);
            if (startGemCount > 1) {
                gemsToBuyCount.put(startGem, startGemCount - 1);
                gemsToBuy.poll();
                startIndex++;
            } else {
                break;
            }
        }
    }

    private void updateAnswer() {
        if (gemsToBuyCount.size() == gemType.size() && answerSize > gemsToBuy.size()) {
            answerStartIndex = startIndex;
            answerSize = gemsToBuy.size();
        }
    }
}
```

