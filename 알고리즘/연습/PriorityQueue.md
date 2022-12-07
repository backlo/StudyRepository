# PriorityQueue

## 우선순위 큐(Prioirty Queue)란?
* 데이터를 꺼낼 때 우선순위가 가장 높은 데이터가 가장 먼저 나오는 구조
* 힙을 이용해서 구현하는 것이 일반적
* 데이터를 삽입할 때 우선순위를 기준으로 최대힙 혹은 최소힙을 구성하고 데이터를 꺼낼 때 루트 노드를 얻어낸다.

#### 우선순위 큐 구현하는 방법
* Comparable 상속하여 구현

``` java
class Person implements Comparable<Person> {
    String name;
    int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public int compareTo(Student target) {
        return this.age <= target.age;
    }
}
```

* Comparator 를 직접 생성하여 구현

``` java
PriorityQueue<Person> pq = new PriorityQueue<>(10, new Comparator<Person>() {
    @Override
    public int compare(Student p1, Student p2) {
       return p1.age >= p2.age;
    }
});
```

#### 중요
* Comparable를 구현하지 않고 우선순위 큐에 넣으면 ClassCastException 발생

#### 구현 (단순 배열)

``` java
public class PriorityQueue {

    private int front;
    private int rear;
    private int queueSize;
    private char[] queueArr;

    public PriorityQueue(int queueSize) {
        front = -1;
        rear = -1;
        this.queueSize = queueSize;
        queueArr = new char[queueSize];
    }

    public boolean isEmpty() {
        if (front == rear) {
            front = -1;
            rear = -1;
            return true;
        }

        return false;
    }

    public boolean isFull() {
        return rear == (this.queueSize - 1);
    }

    public void enqueue(char data) {
        if (isFull()) {
            throw new IllegalArgumentException("큐가 가득 찼습니다.");
        }
        if (isEmpty()) {
            queueArr[++rear] = data;
            return;
        }
        int i;

        for (i = rear; i >= front + 1; i--) {
            if (data > queueArr[i]) {
                queueArr[i + 1] = queueArr[i];
                continue;
            }
            break;
        }

        queueArr[i + 1] = data;
        ++rear;
    }

    public char dequeue() {
        if (isEmpty()) {
            throw new IllegalArgumentException("큐가 비었습니다.");
        }
        front = (front + 1) % this.queueSize;
        return queueArr[front];
    }

    public char peek() {
        if (isEmpty()) {
            throw new IllegalArgumentException("큐가 비었습니다.");
        }
        return queueArr[front];
    }

    public void clear() {
        if (isEmpty()) {
            return;
        }
        front = -1;
        rear = -1;
        queueArr = new char[this.queueSize];
    }

    public void printQueue() {
        if (isEmpty()) {
            System.out.println("큐가 비었습니다.");
            return;
        }
        for (int i = front + 1; i <= rear; i++) {
            System.out.print(queueArr[i] + " ");
        }
        System.out.println();
    }
}

```