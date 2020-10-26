# Think Data Structures

- github : https://github.com/yudong80/ThinkDataStructures



# #1 인터페이스

- 대략적으로 ..인터페이스 위주의 코딩을 하라는거같음



# #2 알고리즘 분석

- List를 구현하는 ArrayList와 LinkedList는 사용처에 따라 성능이 달라질 수 있음
- 어떤 응용프로그램에서 이런 자료구조를 전부활용해서 성능 측정하는것을 프로파일링이라고하는데 단점이 너무 많음
- 알고리즘 분석을 사용하여 이러한 문제점을 해결할 수 있음 몇가지 가정을 해야함
  1. 컴퓨터 하드웨어의 세부사항을 다루지 않기 위해 보통 알고리즘을 이루는 더하기와 곱하기, 숫자 비교 등의 기본 연산을 식별함 그리고 각 알고리즘에 필요한 연산 수를 셈
  2. 입력 데이터의 세부사항을 다루지 않으려면 가장 좋은 선택은 기대하는 입력 데이터에 대한 평균 성능을 분석하는것. 이것이 불가능하면 일반적으로 최악의 시나리오를 분석함
  3. 마지막으로 한 알고리즘이 작은 문제에서는 최상의 성능을 보이지만 큰 문제에서는 다른 알고리즘이 더 좋을 수 있다는 가정을 배제하면안됨 보통 큰 문젱 초점을 둘 것
- 알고리즘을 나누는 범주
  - 상수 시간
    - 실행시간이 입력 크기에 의존하지 않으면 알고리즘은 상수 시간을 따른다고 함 O1
  - 선형
    - 실행시간이 입력 크기에 비례하면 알고리즘은 선형이라고함 On
  - 이차
    - 실행시간이 On2



## 선택 정렬



```java
/**
 * 
 */
package com.allendowney.thinkdast;

import java.util.Arrays;

/**
 * @author downey
 *
 */
public class SelectionSort {

	/**
	 * Swaps the elements at indexes i and j.
	 */
	public static void swapElements(int[] array, int i, int j) {
		int temp = array[i];
		array[i] = array[j];
		array[j] = temp;
	}

	/**
	 * Finds the index of the lowest value
	 * between indices low and high (inclusive).
	 */
	public static int indexLowest(int[] array, int start) {
		int lowIndex = start;
		for (int i = start; i < array.length; i++) {
			if (array[i] < array[lowIndex]) {
				lowIndex = i;
			}
		}
		return lowIndex;
	}

	/**
	 * Sorts the cards (in place) using selection sort.
	 */
	public static void selectionSort(int[] array) {
		for (int i = 0; i < array.length; i++) {
			int j = indexLowest(array, i);
			swapElements(array, i, j);
		}
	}

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		int[] array = {2, 5, 6, 1, 3};
		selectionSort(array);
		System.out.println(Arrays.toString(array));
	}

}
	
```





## 빅오 표기법

- 모든 상수 알고리즘은 O1 
- 선형은 On
- 이차는 On2



# #3 ArrayList 클래스

- get : O1
- set : O1
- indexOf : On
- remove : On
- 일련의 호출에서 평균 시간을 계산하는 알고리즘 분류 방법을 분할 상환 분석이라고함 핵심 개념은 일련의 호출을 하는 동안 배열을 복사하는 추가 비용이 분산되거나 분할 상환되었다는것
- add(T) : O1
- add(int, T) : On
- 문제 크기에 관해 이야기할때 대상이 어떤 크기인지 또는 크기들인지를 주의해야 함 일반적으로 반복문이 한개라면 선형, 두개가 중첩되면 이차인데 반복 횟수가 항상 n에 비례하지 않는다면 좀 더고민해봐야함
- removeAll : Onm
- MyArrayList의 배열은 늘어나기만하고 줄어들지 않음 배열은 가비지 컬렉션을 하지 않고 그 요소도 라스트 자체가 파괴될 때까지 가비지 컬렉션 대상이 아님
- 연결 리스트 구현의 한가지 장점은 요소를 제거하면 리스트 크기가 줄어들고 사용하지 않는 노드는 즉시 가비지 컬렉션이 될 수 있음
- MyLinkedList clear 메서드 본체 (java collection의 linked list가 아님 )

```java
	@Override
	public void clear() {
		head = null;
		size = 0;
	}
```

- head를 null로 바꾸면 head.next -> next.next -> ... 이런식으로 가비지 컬렉터가 동작하기 때문에 선형으로 간주해야 함 이것이 때때로 성능 버그라고 불리는 예임
- 자바는 이런 종류의 버그를 찾기가 어려움 
- 실제로 linkedlist는 null참조를 선형으로 돌려서 지움 그냥 head만 null 시키지 않음





# #4 LinkedList 클래스

- 어떤 연산이 On이 두번나온다고하더라도 중첩이 아니라면 On임



# #5 이중 연결 리스트

- **List와 Deque 인터페이스를 구현하는 이중 연결 리스트 구현, 모든 연산은 이중 연결 리스트와 같이 동작합니다. 리스트의 인덱스를 활용하는 연산은 시작 또는 끝부터 리스트를 순회합니다. 이때 어느 것이든 특정 인덱스에서 가까운 것을 선택합니다.**
- **각 노드는 다음 노드와 이전 노드에 대한 참조를 포함합니다.**
- **LinkedList객체는 첫번째와 마지막 요소에 대한 참조를 포함합니다.**
- 이중 연결 리스트 구현은 ArrayList 클래스보다 시작에 요소를 추가하고 삭제하는 연산이 좋음
- 끝에 요소를 추가하고 제거하는 연산은 ArrayList 클래스와 동일함 
- ArrayList의 유일한 이점은 get과 set 메서드임 
- 연결 리스트는 심지어 이중 연결리스트일 때도 선형 시간이 필요함 get, set
- 응용 프로그램의 실행시간이 get과 set 메서드에 의존한다면 ArrayList 클래스가 좋은 선택임
- 실행 시간이 시작이나 끝 근처에 요소를 추가하거나 제거하는 연산에 의존한다면 LinkedList 클래스가 좋음
- 하지만 이런 추천은 큰 문제의 증가 차수에 기반을 두고 있음 이외에 고려해야 할 요소
  - 이러한 연산이 응용 프로그램의 실행시간에 뚜렷한 영향을 미치지 않는다면, 즉 응용 프로그램이 다른 일을 하느라 대부분 시간을 소모하면 List 구현에 대한 선택은 큰 의미가 없음
  - 작업하는 리스트가 매우 크지 않으면 기대하는 성능을 얻기 어려울지도 모름, 작은 문제에서는 이차 알고리즘이 선형 알고리즘보다 빠르기도 하고 또는 선형 알고리즘이 상수 시간보다 빠르기도 함 작은 문제에서는 그 차이가 그리 중요하지 않음
  - 공간에 대해서는 잊지 말것 지금까지 실행시간에 초점을 맞추었지만 다른 구현은 다른 양의 공간이 필요함 ArrayList에서 요소들은 한 덩어리의 메모리 안에 나란히 저장되어 거의 낭비되는 공간이 없고 컴퓨터 하드웨어도 연속된 덩어리에서 종종 속도가 더 빠름 연결리스트에서 각 요소는 하나 또는 두 개의 참조가 있는 노드가 필요함 참조는 공감을 차지함 (때로는 데이터보다 더 클 수도 ) 메모리 여기저기에 노드가 흩어져 있으면 하드웨어의 효율이 떨어질 수 있음
- 알고리즘 분석은 자료구조를 선택하는 지침을 제공하지만 오직 다음조건일 때만 유효함
  - 응용 프로그램의 실행 시간이 주요하다
  - 응용 프로그램의 실행 시간이 선택한 자료구조에 의존한다.
  - 증가 차수에 따라 어느 자료구조가 나은지 실제로 예측할 수 있을 만큼 문제 크기가 충분히 크다.



# #6 트리 순회



- 검색 엔진의 필수 요소
  - 크롤링: 웹 페이지를 다운로드하고 파싱하고 텍스트와 다른 페이지로의 링크를 추출하는 프로그램
  - 인덱싱: 검색어를 조회하고 해당 검색어를 포함한 페이지를 찾는 데 필요한 자료구조
  - 검색: 인덱스에서 결과를 수집하고 검색어와 가장 관련된 페이지를 식별하는 방법
- jsoup을 사용해서 dom트리를 탐색하기 쉬움
- jsoup.connect 메서드는 string 타입의 url을 인자로 받아 웹 서버에 접속함, get 메서드는 html을 다운로드하여 파싱하고 dom 트리를 나타내는 document 객체를 반환함
- document 객체는 트리를 탐색하고 노드를 선택하는 메서드를 제공함
- 책에서 노드를 선택하는 두가지 방법
  - getElementById는 id 필드를 찾음 해당하는 id의 태그부터 하위 모든 태그를 반환함
  - select 메서드는 string 인자와 일치하는 태그를 모두 찾음
- jsoup에서 Element와 Node가 중요함
- Node 클래스는 dom 트리에서 노드를 의미함 node 하위 클래스에는 Element와 TextNode DataNode, Comment 등이 있음 Elements 클래스는 Elemt 객체의 컬렉션임

<br>

- dfs는 트리의 루트에서 시작하여 첫번째 자식 노드를 선택함. 자식이 자식을 가지고 있다면 첫 번째 자식을 다시 선택함. 자식이 없는 노드에 도착하면 부모 노드로 거슬러 올라가고 부모 노드에 다음 자식이 있다면 그쪽으로 이동함. 다음 자식이 없다면 다시 거슬러 올라가고 루트의 마지막 노드까지 탐색하면 종료함

```java
	private static void recursiveDFS(Node node) {
		if (node instanceof TextNode) {
			System.out.print(node);
		}
		for (Node child: node.childNodes()) {
			recursiveDFS(child);
		}
	}
```

<br>

- 스택은 리스트와 유사한 자료구조로, 요소의 순서를 유지하는 컬렉션임. 스택과 리스트의 주요한 차이점은 스택이 좀 더 적은 메서드를 제공함
- Last in first out
- 스택이 리스트보다 구현 메서드를 줄이는 이유
  - 메서드의 개수를 작게 유지하면 코드는 가독성이 높아지고 오류 발생 가능성도 줄어듬 예를 들어 리스트를 사용해서 스택을 표현하면 실수가 생길 수 있음
  - 자료구조에서 작은 apif를 제공하면 효율적으로 구현하기거 더 쉬움
- 자바로 스택을 구현하는 세가지 선택 사항
  - 기존 ArrayList나 LinkedList 클래스를 사용함 ArrayList를 사용한다면 요소를 끝에 넣고 제거해야 함 이 작업은 상수시간임. 잘못된 위치에 요소를 추가하거나 잘못된 순서로 제거하지 않도록 주의해야 함
  - 자바는 stack 클래스를 제공하여 스택 메서드의 표준 구현을 제공함 하지만 이 클래스는 오래된 자바 버전이여서 jcf와 일치하지 않음
  - 가장 좋은 선택은 ArrayDeque 클래스 같은 Deque 인터페이스를 구현한 클래스를 사용하는 것
- 자바의 Deque 인터페이스는 push, pop, peek, isEmpty를 갖고 있음

```java
	private static void iterativeDFS(Node root) {
		Deque<Node> stack = new ArrayDeque<Node>();
		stack.push(root);

		// if the stack is empty, we're done
		while (!stack.isEmpty()) {

			// otherwise pop the next Node off the stack
			Node node = stack.pop();
			if (node instanceof TextNode) {
				System.out.print(node);
			}

			// push the children onto the stack in reverse order
			List<Node> nodes = new ArrayList<Node>(node.childNodes());
			Collections.reverse(nodes);
			
			for (Node child: nodes) {
				stack.push(child);
			}
		}
	}
```



- LinkedList로 구현할 수도 있음
- LinkedList는 List와 Deque 둘 다 구현했음





---





# #9 Map 인터페이스

- 해시 테이블은 지금까지 발명된 자료구조중 단연 으뜸임



# #10 해싱

- 해시함수를 사용해서 같은 객체에 대해서는 항상 같은 해시코드를 반환하도록 하게 함
- 해시 함수의 근본적인 요구사항은 같은 객체는 매번 같은 해시코드를 만들어야 한다는 것. 불변 객체일때는 상대적으로 쉽지만 객체가 가변일때는 조금 고민해봐야 함
- hasCode와 equlas는 항상 같은 값을 반환해야함, eqauls가 true라면 두 객체의 해시 코드 또한 같아야함
- 하지만 두 객체의 해시 코드가 같더라도 그들이 같은 객체일 필요는 없음
- 해시함수의 목표 중 하나는 균등해야 함 일정 범위에 있는 어떤 값으로 고루 퍼지도록 해시 코드를 생성해야함
- 해싱을 사용하는 자료구조에서 가변 객체를 키로 사용하는 것은 위험함. 키가 맵에 있는 동안 변형되지 안흔ㄴ다고 보장할 수 있거나 어떤 변화가 해시 코드에 영향을 미치지 않으면 괜찮음





# #11 HashMap 클래스

- uml 클래스 다이어그램
  - 머리가 꽉 찬 화살표는 has-A 관계, 화살표 머리가 있는 곳을 포함하고 있다는 뜻
  - 빈 머리와 실선의 화살표는 is - A 관계 화살표 머리가 있는 곳을 확장하고 있다는 뜻
  - 빈 머리와 점선의 화살표는 인터페이스를 구현하고 있다는 뜻

- hashmap을 보고 정리한 결과
  - HashMap은 필드중에 단방향 연결 리스트 Node<K, V>[] 를 갖고 있음
  - 만약 key 값이 들어오면 key의 적절한 해시함수를 사용해서 인덱스를 결정함
  - 이 인덱스 값을 기반으로 Node<K, V>[] 내부에 새로운 Node<K, V> 값을 지정함
  - 만약 인덱스 값이 겹친다면 Node<K, V> 내부에 연결리스트로 묶어줌
  - 그래서 해싱이 적절하게 구현되어야 좋은 성능이 나올 수 있음
  - 만약 기존 케파값보다 작다면 다시 적절한 값으로 리사이징해서 Node<K, V>[] 의 크기를 늘림
  - 이런식으로 항상 상수값에 가까운 put을 보여줌
  - get도 마찬가지로 적절한 해시값을 통해서 인덱스를 먼저 구하고 Node<K, V>[] 에서 데이터를 찾아서 거의 상수값에 가깝도록 리턴함

# #12 TreeMap 클래스

- 해싱의 문제점
  - HashMap 클래스의 연산이 상수 시간이라고 하더라도 해싱이 느릴 수 있음 즉, 상수가 꽤 커질 수 있음
  - 해시 함수가 하위 맵에 키를 고루 분배하면 해싱은 잘 동작함. 하지만 좋은 해시 함수를 설계하는 것은 쉬운일이 아님. 그리고 너무나 많은 키가 특정 하위 맵에 집중되면 hashMap의 성능이 나빠질 수 있음
  - 해시 테이블에 있는 키는 어떤 순서대로 저장되지 않음  사실 순서는 테이블이 커지고 키가 재해시될 때 변하기도함, 어떤 응용 프로그램에서는 키를 순서대로 유지하는 것이 필요하거나 적어도 유용함
  - 이모든 문제를 해결하기는 어렵지만 거의 해결해줄 수 있는 TreeMap을 제공함
- TreeMap
  - TreeMap 클래스는 해시 함수를 사용하지 않음 따라서 해싱 비용과 해시 함수를 고르는 어려움을 피할 수 있음
  - TreeMap 클래스의 내부에서 키는 이진 탐색 트리에 저장되는데 선형 시간으로 키를 순서대로 순회할 수 있음
  - 핵심 메서드의 실행 시간은 log n 에 비례하여 상수 시간만큼 우수하지 않지만 꽤 쓸만함
- 이진탐색 트리
  - 노드 왼쪽에 자식이 있다면 왼쪽 하위의 모든 키는 노드에 있는 키보다 작음
  - 노드 오른쪽에 자식이 있다면 오른쪽 하위 트리의 모든 키는 노드에 잇는 키보다 큼
  - 트리 전체를 검색할 필요가 없어서 이진 탐색 트리에 있는 키의 검색 속도는 빠름
  - 루트에서 시작하여 다음과 같은 알고리즘을 사용할 수 있음
    - 찾는 키인 target을 현재 노드의 키와 비교함 같다면 검색 완료
    - target이 현재 키보다 작으면 왼쪽 트리를 검색함 왼쪽 트리에 없다면 target은 없음
    - target이 현재 키보다 크면 오른쪽 트리를 검색함. 오른쪽 트리가 없다면 target은 트리에 없음
  - 비교횟수는 일반적으로 키의 개수가 아니라 트리의 높이에 비례함
  - 전체 노드의 개수는 (height x 2) - 1
  - 트리는 로그시간이 걸리고 증가차수는 O(log n)임



# #13 이진 탐색 트리

- 단순히 이진트리를 만들면 값이 무조건 증가 무조건 감소할때 검색 성능이 선형으로나오는 문제점이 있음
- 이 문제를 해결하는 방법
  - map 객체에 키를 순서대로 넣지 않으면 됨. 하지만 항상 가능하지 않음
  - 키가 순서대로 들어오더라도 이를 대응하도록 트리를 만들 수 있음
- 두번째방법이 더 좋고 이 해법을 실행할 수 있는 몇가지 방법이 있음
- 가장 일반적인 방법은 put 메서드를 고쳐서 트리가 불균형해질 때 이를 탐지하고 불균현 하다면 노드를 재배열하는 것임
- 이 기능을 자가 균형이라고 하는데 일반적인 자가 균형 트리에는 avl트리와 자바의 treemap 클래스가 사용하는 레드블랙 트리가 있음
- 요약하면 이진 탐색 트리는 로그 시간으로 get put을 구현할 수 있지만 이때는 트리가 충분히 균형을 유지하는 순서로 키를 추가해야함. 자가 균형 트리는 새로운 키가 추가될 때 추가적인 작업을 함으로써 이러한 문제를 회피할 수 있음



# #14 영속성









































