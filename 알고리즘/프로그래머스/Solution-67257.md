# 프로그래머스 - 수식 최대화

<hr>

문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/67257](https://programmers.co.kr/learn/courses/30/lessons/67257)

* Java

```java
import java.util.ArrayList;
import java.util.List;

public class Solution67257 {

    private static final List<String> operators = new ArrayList<>();
    private static final List<Long> numbers = new ArrayList<>();
    private static final String[] suggestOperators = {"+", "-", "*"};
    private static final boolean[] visited = new boolean[3];
    private static final String[] resultOperators = new String[3];
    private long answer = 0;

    public long solution(String expression) {
        StringBuilder number = new StringBuilder();

        for (int i = 0; i < expression.length(); i++) {
            char character = expression.charAt(i);
            if (character == '+' || character == '-' || character == '*') {
                operators.add(String.valueOf(character));
                numbers.add(Long.parseLong(number.toString()));
                number = new StringBuilder();
                continue;
            }
            number.append(character);
        }

        numbers.add(Long.parseLong(number.toString()));
        permutation(0, suggestOperators.length);

        return answer;
    }

    private void permutation(int depth, int size) {
        if (depth == size) {
            resolve();
            return;
        }

        for (int i = 0; i < suggestOperators.length; i++) {
            if (!visited[i]) {
                visited[i] = true;
                resultOperators[depth] = suggestOperators[i];
                permutation(depth + 1, size);
                visited[i] = false;
            }
        }
    }

    private void resolve() {
        List<String> operatorTemps = new ArrayList<>(operators);
        List<Long> numberTemps = new ArrayList<>(numbers);

        for (String currentOperator : resultOperators) {
            for (int j = 0; j < operatorTemps.size(); j++) {
                if (operatorTemps.get(j).equals(currentOperator)) {
                    long number1 = numberTemps.get(j);
                    long number2 = numberTemps.get(j + 1);
                    long result = calculate(number1, number2, currentOperator);

                    numberTemps.remove(j + 1);
                    numberTemps.remove(j);
                    operatorTemps.remove(j);

                    numberTemps.add(j, result);
                    j--;
                }
            }
        }
        answer = Math.max(answer, Math.abs(numberTemps.get(0)));
    }

    private long calculate(long n1, long n2, String o) {
        long res = 0;
        switch(o) {
            case "+" :
                res = n1 + n2;
                break;
            case "-" :
                res = n1 -n2;
                break;
            case "*" :
                res = n1 * n2;
                break;
        }
        return res;
    }
}
```

