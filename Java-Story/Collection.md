# Collection

- 여러 데이터의 묶음이 컬렉션
- 컬렉션은 추상체
  - List
  - LinkedList
  - ArrayList
  - Stack
  - Vector
  - Set

# Iterator

```java
    MyIterator<String> iter =
            new MyCollection<>(Arrays.asList("A", "CA", "DSB", "ASDC", "ASDFE"))
                    .iterator();

    while(iter.hasNext()) {
        String s = iter.next();
        int len = s.length();
        if(len % 2 == 0) continue;
        System.out.println(s);
    }
```
- 여러 데이터의 묶음을 풀어서 하나씩 처리할 수 있는 수단을 제공
- next로 다음 데이터를 조회한다. 데이더가 없으면 예외가 터진다. 
- 정방향으로만 데이터 조회가 가능하다. (이전 데이터 조회 불가능)

# Stream

- System.in = InputStream;
- System.out = OutputStream; 
- java 8 이상에서부터 활용 가능(collection 데이터에 대해 stream을 제공해주게 된것)
- filter,map,forEach 같은 고차함수(함수를 인자로 받는 함수)가 제공된다.
- 연속 데이터를 풍부한 고차함수로 많은 기능을 간결하게 사용할 수 있다.

### 생성방법
- Stream.generate(); => Supplier처럼 생성 
- Stream.iterate(); => Function처럼 생성

# Optional
- 자바에서는 모든 것이 레퍼런스 => 모든것이 null이 될 수 있다
- 이제부터 null을 사용하지 않도록 약속하고 프로그래밍 하는 방식
- null 데이터 : User.empty();
- 데이터 : Optional.of(DATA)
- isEmpty, isPresent로 확인한다. 
