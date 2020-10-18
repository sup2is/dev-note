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
- 































