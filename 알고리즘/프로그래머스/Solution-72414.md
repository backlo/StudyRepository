# 프로그래머스 - 광고 삽입

<hr>


문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/72414](https://programmers.co.kr/learn/courses/30/lessons/72414)

* Java

```java
class Solution72414 {
    public String solution(String play_time, String adv_time, String[] logs) {
        int playTime = convertSecond(play_time);
        int advTime = convertSecond(adv_time);
        int[] counts = new int[playTime + 1];

        for (String log : logs) {
            String[] logStr = log.split("-");
            int startTime = convertSecond(logStr[0]);
            int endTime = convertSecond(logStr[1]);

            for (int i = startTime; i < endTime; i++) {
                counts[i]++;
            }
        }

        int advStartTime = 0;
        int advEndTime = advTime;
        long sum = 0;
        for (int i = advStartTime; i < advEndTime; i++) {
            sum += counts[i];
        }

        long max = sum;
        int maxStartTime = 0;
        while(advEndTime <= playTime) {
            sum -= counts[advStartTime];
            sum += counts[advEndTime];

            if (sum > max) {
                max = sum;
                maxStartTime = advStartTime + 1;
            }

            advStartTime++;
            advEndTime++;
        }

        return convertTime(maxStartTime);
    }

    private int convertSecond(String time) {
        String[] times = time.split(":");
        return Integer.parseInt(times[0]) * 3600 + Integer.parseInt(times[1]) * 60 + Integer.parseInt(times[2]);
    }

    private String convertTime(int time) {
        int hour = time / 3600;
        time %= 3600;
        int minute = time / 60;
        int second = time % 60;

        String hourStr = hour > 9 ? String.valueOf(hour) : "0" + hour;
        String minuteStr = minute > 9 ? String.valueOf(minute) : "0" + minute;
        String secondStr = second > 9 ? String.valueOf(second) : "0" + second;

        return String.join(":", hourStr, minuteStr, secondStr);
    }
}
```

