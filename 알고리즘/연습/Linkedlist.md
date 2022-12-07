# LinkedList

## LinkedList 란?
* 연결리스트는 각 노드가 데이터와 포인터를 가지고 한줄로 연결되어 있는 방식의 자료구조
* 데이터를 담고 있는 노드들이 연결되어 있고, 토드의 포인터가 이전, 다음 노드와의 연결을 담당

## 연결리스트 장점, 단점
* 장점
	* 배열의 비해 추가/삭제가 용이
* 단점
	* 순차 탐색을 필요로 해 탐색 속도가 떨어짐
* 즉 탐색, 정렬을 많이 하는 경우 배열을 사용하고, 데이터 추가/삭제가 많은 경우 연결리스트를 사용

## 연결 리스트의 종류
* 단일 연결 리스트 
	*  각 노드가 다음 노드에 대해서만 참조하는 단순한 형태의 구조
	*  Head노드를 잃어버려 참조 못할 경우 데이터 전체를 사용 못하는 단점이 있음
* 이중 연결 리스트 
	* 각 노드가 이전 노드, 다음 노드에 대해서 참조하는 연결 리스트
	* 삭제가 간편, 데이터 손상이 강함
	* 하지만 관리할 참조가 2개이기 때문에 삽입이나 정렬의 경우 작업량이 더 많아진다는 단점이 있음
* 원형 연결 리스트 
	* 연결 리스트에서 마지막 요소가 첫번째 요소를 참조
	* 스트림 버퍼에 많이 사용

## 구현 코드
``` java
public class LinkedList {

    private Node head;

    public LinkedList() {
        this.head = null;
    }

    public void insertNode(String data) {
        Node newNode = new Node(data);

        if (this.head == null) {
            this.head = newNode;
            return;
        }

        Node tempNode = head;

        while (tempNode.getLink() != null) {
            tempNode = tempNode.getLink();
        }

        tempNode.setLink(newNode);
    }

    public void insertNode(Node preNode, String data) {
        Node newNode = new Node(data);

        newNode.setLink(preNode.getLink());
        preNode.setLink(newNode);
    }

    public Node searchNode(String data) {
        Node tempNode = head;

        while (tempNode.getLink() != null) {
            if (data.equals(tempNode.getData())) {
                return tempNode;
            }
            tempNode = tempNode.getLink();
        }

        throw new IllegalArgumentException("찾는 데이터가 없습니다.");
    }

    public void deleteNode(String data) {
        if (head == null) {
            throw new IllegalArgumentException("빈 리스트 입니다.");
        }

        Node preNode = head;

        if (data.equals(preNode.getData())) {
            head = preNode.getLink();
            preNode.setLink(null);
            return;
        }

        Node nextNode = preNode.getLink();

        while (nextNode != null) {
            if (data.equals(nextNode.getData())) {
                if (nextNode.getLink() == null) {
                    preNode.setLink(null);
                } else {
                    preNode.setLink(nextNode.getLink());
                    nextNode.setLink(null);
                }
                break;
            }
            preNode = nextNode;
            nextNode = nextNode.getLink();
        }
    }

    public void deleteNode() {
        if (head == null) {
            throw new IllegalArgumentException("빈 리스트 입니다.");
        }
        if (head.getLink() == null) {
            head = null;
            return;
        }

        Node preNode = head;
        Node nextNode = preNode.getLink();

        while (nextNode.getLink() != null) {
            preNode = nextNode;
            nextNode = nextNode.getLink();
        }

        preNode.setLink(null);
    }

    public void reverseList() {
        Node preNode = null;
        Node currentNode = null;
        Node nextNode = head;

        while (nextNode != null) {
            preNode = currentNode;
            currentNode = nextNode;
            nextNode = nextNode.getLink();

            currentNode.setLink(preNode);
        }

        this.head = currentNode;
    }

    public void printList() {
        Node tempNode = head;

        while (tempNode != null) {
            System.out.print(tempNode.getData() + " ");
            tempNode = tempNode.getLink();
        }

        System.out.println();
    }
}
```