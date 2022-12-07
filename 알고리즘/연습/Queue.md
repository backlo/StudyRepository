# Queue

## 큐(Queue)란?
* 먼저 집어넣은 데이터가 먼저 나오는 FIFO 구조
* 스택과 반대되는 개념

#### 큐의 속성 알아보기
* add vs offer
	* add: 큐 사이즈가 넘어갈 경우 예외를 던지면서 실패
	* offer: 큐 사이즈가 넘어갈 경우 false 리턴 
* remove vs poll
	* remove: 빈 큐에서 예외를 던짐
	* poll: null 리턴
* element vs peek
	* element: 빈 큐에서 예외를 던짐
	* peek: null 리턴

#### 구현

* 연결 리스트로 구현
``` java
public class Queue implements QueueInterface {

    private Node head;
    private Node front;
    private Node rear;
    private int size;

    public Queue() {
        this.head = null;
        this.front = null;
        this.rear = null;
        this.size = -1;
    }

    public Queue(int size) {
        this.head = null;
        this.front = null;
        this.rear = null;
        this.size = size;
    }

    @Override
    public boolean isEmpty() {
        return front == null && rear == null;
    }

    @Override
    public boolean isFull() {
        if (isEmpty()) {
            return false;
        }
        int nodeCount = 0;
        Node tempNode = this.head;

        while (tempNode != null) {
            ++nodeCount;
            tempNode = tempNode.getLink();
        }

        return nodeCount == (this.size - 1);
    }

    @Override
    public void enqueue(String data) {
        Node newNode = new Node(data);
        if (isEmpty()) {
            this.head = newNode;
            this.front = this.head;
            this.rear = this.head;
            return;
        }

        if (isFull() && this.size != -1) {
            throw new IllegalArgumentException("큐가 가득차서 넣을수 없습니다.");
        }

        this.rear.setLink(newNode);
        this.rear = newNode;
    }

    @Override
    public String dequeue() {
        String data;

        if (isEmpty()) {
            throw new IllegalArgumentException("큐가 비었습니다.");
        }

        if (this.front.getLink() == null) {
            data = this.front.getData();
            this.head = null;
            this.front = null;
            this.rear = null;
            return data;
        }

        data = this.front.getData();

        this.head = this.front.getLink();
        this.front.setLink(null);
        this.front = this.head;

        return data;
    }

    @Override
    public String peek() {
        if (isEmpty()) {
            throw new IllegalArgumentException("큐가 비었습니다.");
        }
        return front.getData();
    }

    @Override
    public void clear() {
        this.head = null;
        this.front = null;
        this.rear = null;
    }

    public Node searchNode(String data) {
        Node tempNode = this.front;

        while (tempNode != null) {
            if (data.equals(tempNode.getData())) {
                return tempNode;
            }
            tempNode = tempNode.getLink();
        }

        throw new IllegalArgumentException("찾는 데이터가 없습니다.");
    }

    public void printQueue() {
        if (isEmpty()) {
            System.out.println("큐가 비었습니다.");
            return;
        }

        Node tempNode = this.front;
        while (tempNode != null) {
            System.out.print(tempNode.getData() + " ");
            tempNode = tempNode.getLink();
        }

        System.out.println();
    }
}
```