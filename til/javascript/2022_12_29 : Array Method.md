## 배열의 메서드들
 * 정적 매서드
   1. `Array.from()` : 유사 배열 또는 반복 가능한 객체로부터 새로운 Array 인스터스를 생성
   
   2. `Array.isArray()` : 매개변수가 배열이면 true를 아니면 false를 반환
   
   3. `Array.of()` : 매개변수의 수와 자료형에 제한없이, 지정한 값을 사용해 새 Array 인스턴스를 생성
   
 * 인스턴스 속성
   
   1. `Array.prototype.length` : 배열의 원소 개수를 출력
 
 * 인스턴스 메서드
 
   1. `.at()` : 주어진 인덱스의 요소를 반환. 음수 값을 지정할 경우 인덱스를 배열의 끝부터 센다.
   
   2. `.concat()` : 배열과 배열/값 매개변수를 이어붙인 새로운 배열을 반환
   3. `.copyWithin()` : 배열 내의 지정된 요소들을 동일한 배열 내에서 복사
   4. `.entries()` : 배열의 각 인덱스에 대한 키/값 쌍을 가지는 새로운 배열 반복자 객체를 반환
   5. `.every()` : 배열의 모든 요소가 주어진 판별 함수를 만족할 경우 true를 반환
   6. `.fill()` : 배열을 시작 인덱스부터 끝 인덱스까지 지정한 값으로 채움
   7. `.filter()` : 지정한 필터 함수의 반환 결과가 true인 요소만 모아서 새로운 배열을 반환
   8. `.find()` : 주어진 판별 함수를 만족하는 첫 번째 요소를 반환, 만족하는 요소가 없으면 undefined를 반환
   9. `.findIndex()` : 주어잔 판별 함수를 만족하는 첫 번째 요소의 인덱스를 반환, 없으면 -1반환
   10. `.flat()` : 배열 내의 모든 중첩 배열을 지정한 깊이까지 재귀적으로 이어붙인 새로운 배열을 반환
   11. `.flatMap()` : 배열의 모든 요소 가가에 대해 주어진 콜백 함수를 호출하고 그 반환 값을 모아 새로운 배열을 생성한 후, 모든 중첩 배열을 이어붙여 평탄화해 반환
   12. `.forEach()` : 배열의 각각의 요소에 대해 함수를 호출
   13. `.includes()` : 배열이 주어진 값을 포함하는지 판별해 true 또는 false를 반환
   14. `.indexOf()` : 배열에서 주어진 값과 일치하는 제일 앞의 인덱스를 반환, 없으면 -1 반환
   15. `.join()` : 배열의 모든 요소를 문자열로 합침
   16. `.keys()` : 배열의 각 인덱스에 대한 키를 가지는 새로운 배열 반복자 객체를 반환
   17. `.lastIndexOf()` : 배열에서 주어진 값과 일치하는 제일 뒤의 인덱스를 반환, 없으면 -1 반환
   18.  `.map()`  : 배열의 모든 요소 각각에 대하여 주어진 콜백 함수를 호출하고, 그 반환 값을 모은 새로운 배열을 반환
   19. `.pop()` : 배열에서 마지막 요소를 뽑아내고, 그 요소를 반환
   20. `.push()` : 배열의 끝에 하나 이상의 요소를 추가하고, 배열의 변경된 length를 반환
   21. `.reduce()` : 주어진 콜백 함수를 가산기와 요소 각각에 대해 (왼쪽에서 오른쪽으로) 호출하여 하나의 값으로 줄인 결과를 반환
   22. `.reduceRight()` : 주어진 콜백 함수를 가산기와 요소 각각에 대해 (오른쪽에서 왼쪽으로) 호출하여 하나의 값으로 줄인 결과를 반환
   23. `.reverse()` : 배열의 요소 순서를 뒤집는다.
   24. `.shift()` : 배열에서 첫 번째 요소를 삭제하고 그 요소를 반환
   25. `.slice()` : 배열의 일부를 추출한 새 배열을 반환
   26. `.some()` : 배열의 어떤 요소가 주어진 판별 함수를 만족할 경우 true를 반환
   27. `.sort()` : 배열의 요소를 정렬하고 그 배열을 반환
   28. `.splice()` : 배열에서 요소를 추가하거나 삭제
   29. `.unshift()` : 배열의 앞에 하나 이상의 요소를 추가하고, 배열의 변경된 length를 반환
   30. `.values()` : 배열의 각 인덱스에 대한 값을 가지는 새로운 배열 반복자 객체를 반환
   
> 오늘은 배열과 매서드쪽 진도를 나갔는데, 빨리 진도를 나가고 this, arguments를 개인적으로 공부했다. this의 개념이 조금 잡혀가던 와중에 화살표함수(arrow function)가 this, arguments를 가지지 않는다는 것을 알게되었고, 이해가 될랑말랑해서 미칠뻔했다. 언젠가 날을 잡고, 조금 상세하게 다뤄봐야 할 것 같다.