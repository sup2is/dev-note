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





































