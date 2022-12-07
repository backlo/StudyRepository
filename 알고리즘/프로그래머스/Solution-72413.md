# 프로그래머스 - 합승 택시 요금

<hr>


문제링크 : [https://programmers.co.kr/learn/courses/30/lessons/72413](https://programmers.co.kr/learn/courses/30/lessons/72413)

* Java - 플로이드 와샬 버전 (동시 탑승을 구할 때 추천하는 알고리즘 (a, b가 동시 탑승하고 목적지가 다를 때))

```java
public class Solution72413 {

    public int solution(int n, int s, int a, int b, int[][] fares) {
        int answer = Integer.MAX_VALUE;
        int[][] nodes = new int[n + 1][n + 1];

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                nodes[i][j] = 100000 * n;
                if (i == j) nodes[i][j] = 0;
            }
        }

        for (int[] fare : fares) {
            nodes[fare[0]][fare[1]] = fare[2];
            nodes[fare[1]][fare[0]] = fare[2];
        }

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                for (int k = 1; k <= n; k++) {
                    nodes[j][k] = Math.min(nodes[j][k], nodes[j][i] + nodes[i][k]);
                }
            }
        }

        for (int i = 1; i <= n; i++) {
            answer = Math.min(answer, nodes[s][i] + nodes[i][a] + nodes[i][b]);
        }

        return answer;
    }
}
```

* 자바 - 다익스트라 버전 (개인 탑승을 할때 추천하는 알고리즘 (a, b, s 따로 구해짐))

```java
import java.util.*;

class Solution72413 {
    public int solution(int n, int s, int a, int b, int[][] fares) {
        int answer = Integer.MAX_VALUE;
        List<List<Edge>> graph = new ArrayList<>();
        int[] costA = new int[n + 1];
        int[] costB = new int[n + 1];
        int[] costS = new int[n + 1];

        for (int i = 0; i <= n; i++) {
            graph.add(new ArrayList<>());
            costA[i] = 100000 * n;
            costB[i] = 100000 * n;
            costS[i] = 100000 * n;
        }

        for (int[] fare : fares) {
            graph.get(fare[0]).add(new Edge(fare[1], fare[2]));
            graph.get(fare[1]).add(new Edge(fare[0], fare[2]));
        }

        foundMinCost(a, costA, graph);
        foundMinCost(b, costB, graph);
        foundMinCost(s, costS, graph);

        for (int i = 0; i <= n; i++) {
            answer = Math.min(answer, costA[i] + costB[i] + costS[i]);
        }

        return answer;
    }

    private void foundMinCost(int startNode, int[] costs, List<List<Edge>> graph) {
        PriorityQueue<Edge> pq = new PriorityQueue<>(graph.get(startNode));

        costs[startNode] = 0;
        for (Edge edge : graph.get(startNode)) {
            costs[edge.next] = edge.cost;
        }

        while (!pq.isEmpty()) {
            Edge currentEdge = pq.poll();
            if (costs[currentEdge.next] < currentEdge.cost) {
                continue;
            }
            for (Edge nextEdge : graph.get(currentEdge.next)) {
                if (costs[nextEdge.next] > currentEdge.cost + nextEdge.cost) {
                    costs[nextEdge.next] = currentEdge.cost + nextEdge.cost;
                    pq.add(new Edge(nextEdge.next, currentEdge.cost + nextEdge.cost));
                }
            }
        }
    }

    static class Edge implements Comparable<Edge> {
        private final int next;
        private final int cost;

        public Edge(int next, int cost) {
            this.next = next;
            this.cost = cost;
        }

        @Override
        public int compareTo(Edge edge) {
            return this.cost - edge.cost;
        }
    }
}
```

