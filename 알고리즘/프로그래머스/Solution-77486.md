# 프로그래머스 - 다단계 칫솔 판매

<hr>

문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/77486](https://programmers.co.kr/learn/courses/30/lessons/77486)

* Java (자신이 Parent를 가지고 있는 경우)

```java
import java.util.*;

public class Solution77486 {
    
    public int[] solution(String[] enroll, String[] referral, String[] seller, int[] amount) {
        Map<String, Person> people = new HashMap<>();
        int[] answer = new int[enroll.length];

        people.put("-", new Person("-"));
        for (String enrollPerson : enroll) {
            people.put(enrollPerson, new Person(enrollPerson));
        }

        for (int i = 0; i < referral.length; i++) {
            people.get(enroll[i]).setParent(people.get(referral[i]));
        }

        for (int i = 0; i < seller.length; i++) {
            earnMoney(people.get(seller[i]), amount[i] *  100);
        }

        for (int i = 0; i < enroll.length; i++) {
            answer[i] = people.get(enroll[i]).getEarnMoney();
        }

        return answer;
    }

    private void earnMoney(Person person, int earnMoney) {
        int earnParentMoney = earnMoney / 10;
        if (earnParentMoney != 0 && person.getParent() != null) {
            person.setEarnMoney(person.getEarnMoney() + earnMoney - earnParentMoney);
            earnMoney(person.getParent(), earnParentMoney);
        } else {
            person.setEarnMoney(person.getEarnMoney() + earnMoney);
        }
    }

    static class Person {
        private final String name;
        private int earnMoney;
        private Person parent;

        public Person(String name) {
            this.name = name;
            this.earnMoney = 0;
            this.parent = null;
        }

        public void setParent(Person parent) {
            this.parent = parent;
        }

        public Person getParent() {
            return parent;
        }

        public int getEarnMoney() {
            return earnMoney;
        }

        public void setEarnMoney(int earnMoney) {
            this.earnMoney = earnMoney;
        }
    }
}
```

* Java - 타임아웃 코드 (자신이 Child를 가지고 있는 경우)

```java
import java.util.*;

public class Solution77486 {
    public int[] solution(String[] enroll, String[] referral, String[] seller, int[] amount) {
        Person center = new Person("-");
        int[] answer = new int[enroll.length];

        for (int i = 0; i < referral.length; i++) {
            Person person = new Person(enroll[i]);
            center.addLowPerson(referral[i], person);
        }

        for (int i = 0; i < seller.length; i++) {
            List<Person> chainPersons = new ArrayList<>();
            chainPersons.add(center);
            String sellerPerson = seller[i];
            center.earnMoney(sellerPerson, amount[i], chainPersons);
        }

        for (int i = 0; i < enroll.length; i++) {
            answer[i] = center.getEarnMoney(enroll[i]);
        }

        return answer;
    }

    static class Person {
        private final String name;
        private int earn = 0;
        private final List<Person> lowPersons = new ArrayList<>();

        public Person(String name) {
            this.name = name;
        }

        public void addLowPerson(String referral, Person person) {
            if (referral.equals(name)) {
                lowPersons.add(person);
                return;
            }
            for (Person low : lowPersons) {
                low.addLowPerson(referral, person);
            }
        }

        public void earnMoney(String sellerPerson, int amount, List<Person> chainPersons) {
            for (Person lowPerson : lowPersons) {
                if (lowPerson.getName().equals(sellerPerson)) {
                    chainPersons.add(lowPerson);
                    calculateMoney(chainPersons, amount);
                    return;
                }
                chainPersons.add(lowPerson);
                lowPerson.earnMoney(sellerPerson, amount, chainPersons);
                chainPersons.remove(chainPersons.size() - 1);
            }
        }

        public void calculateMoney(List<Person> chainPersons, int amount) {
            float earnMoney = amount * 100;
            for (int i = chainPersons.size() - 1; i >= 0; i--) {
                if (i == 0) {
                    chainPersons.get(i).earn = (int) (chainPersons.get(i).earn + earnMoney);
                    break;
                }
                int interestRate = (int) Math.floor(earnMoney / 10);
                int restRate = (int) (earnMoney - interestRate);
                chainPersons.get(i).earn = chainPersons.get(i).earn + restRate;
                earnMoney = interestRate;
            }
        }

        public String getName() {
            return name;
        }

        public int getEarnMoney(String name) {
            if (this.name.equals(name)) {
                return this.earn;
            }
            for (Person lowPerson : lowPersons) {
                int findPerson = lowPerson.getEarnMoney(name);
                if (findPerson == -1) {
                    continue;
                }
                return findPerson;
            }
            return -1;
        }
    }
}
```

