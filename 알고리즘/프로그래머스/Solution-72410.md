# 프로그래머스 - 신규 아이디 추천

<hr>


문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/72410](https://programmers.co.kr/learn/courses/30/lessons/72410)

* Java

```java
class Solution72410 {
    public String solution(String new_id) {
        String answer = step1(new_id);
        answer = step2(answer);
        answer = step3(answer);
        answer = step4(answer);
        answer = step5(answer);
        answer = step6(answer);
        answer = step7(answer);
        return answer;
    }

    private String step1(String id) {
        return id.toLowerCase();
    }

    private String step2(String id) {
        StringBuilder changeId = new StringBuilder();
        for(int i = 0; i < id.length(); i++) {
            char ch = id.charAt(i);

            if(ch >= 'a' && ch <= 'z') {
                changeId.append(ch);
            }
            if(ch >= '0' && ch <= '9') {
                changeId.append(ch);
            }
            if(ch == '.' || ch == '-' || ch == '_') {
                changeId.append(ch);
            }
        }
        return changeId.toString();
    }

    private String step3(String id) {
        return id.replaceAll("[.]{2,}", ".");
    }

    private String step4(String id) {
        if (id.length() == 0) {
            return id;
        }
        if (id.charAt(0) == '.') {
            id = id.substring(1);
        }
        if (id.length() == 0) {
            return id;
        }
        if (id.charAt(id.length() - 1) == '.') {
            id = id.substring(0, id.length() - 1);
        }
        return id;
    }

    private String step5(String id) {
        if (id.length() == 0) {
            return "a";
        }
        return id.replaceAll("[\\s]", "a");
    }

    private String step6(String id) {
        if (id.length() > 15) {
            id = id.substring(0, 15);
        }
        if (id.endsWith(".")) {
            id = id.substring(0, id.length() -1);
        }
        
        return id;
    }

    private String step7(String id) {
        StringBuilder sb = new StringBuilder(id);
        if (id.length() <= 2) {
            while(sb.length() != 3) {
                sb.append(id.charAt(id.length() - 1));
            }
        }
        return sb.toString();
    }
}
```
