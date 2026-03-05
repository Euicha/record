# High-Performance SQL
# Join Types  
SQL에서 JOIN은 서로 다른 테이블의 컬럼들을 결합한 결과를 만들어 준다.  
  
- CROSS JOIN  
- INNER JOIN  
- OUTER JOIN  
- NATURAL JOIN  
  
## CROSS JOIN
cartesian product와 같은 결과.  
```sql
-- SQL:92
select
    ranks.name as rank,
    suits.symbol as suit
from
    ranks
cross join
    suits
order by
    ...

--- SQL:89 theta-style syntax
select
    ranks.name as rank,
    suits.symbol as suit
from
    ranks, suits
    ...
```  
## INNER JOIN  
inner join은 두 테이블을 조인하여 부모와 자식에 대한 프로젝션(열 단위로 원하는 데이터를 조회)을 생성할 수 있게 해준다.  
```sql
select 
    p.id as post_id,
    p.title as post_title,
    pc.review as review
from
    post p
inner join
    post_comment pc on pc.post_id = p.id
order by
    pc.id
```
필터링을 하면서 댓글이 있는 게시물만 join하도록 지정하고 있다. 일종의 필터링된 Cartesian 곱과 같다고 할 수 있다.  
on 절을 사용하여 관계를 정의하고 외래 키 컬럼과 기본 키 컬럼을 매칭하여 조인 결과 집합을 얻었다.  
  
on을 사용할 때와 using을 사용할 때 약간의 차이가 존재한다.  
select *를 할 때 on절은 양쪽 테이블 컬럼이 다 나오고, 조인 컬럼이 중복되지만 using은 조인에 사용된 컬럼은 한 번만 나온다.  
  
# JOIN Performances
서브쿼리와 조인 중 무엇을 사용해야 할까?  
  
```sql
select distinct
s.id, s.first_name, s.last_name
from student s
inner join student_grade sg
on sg.student_id = s.id
where sg.grade = 10
order by s.id
```
이러한 유형의 쿼리가 자주 나오는 것은 일반적이다.  
Hash Join, 5천 건의 레코드에 대해 4 ms 소요. Hash Join은 버킷을 생성해야 하므로 시간이 걸린다. 마지막에 정렬을 수행하여 최종적으로 학생들만 얻는다.  
  
요구사항으로 다시 돌아가 보면 학생들만 필요하다.  
```sql
select 
s.id, s.first_name, s.last_name
from student s
where exists (
    select 1
    from student_grade sg
    where sg.student_id = s.id and sg.grade = 10
) order by id
```
이 방법은 Semi Join을 사용한다. 스캔한 row 수가 줄었는데, 10이라는 값을 얻으면 조인 실행을 건너뛰고 다음 사용자로 넘어갈 수 있기 때문이다.  
  
복합 프로젝션이 필요하다면 당연히 조인을 사용해야 한다. 하지만 필터링만 하고 싶고 한 테이블만 프로젝션 하려면 서브쿼리를 사용하는 것이 조인보다 효율적일 수 있다.  
  
## JOIN Algorithms
1. Nested Loops  
2. Hash Join  
3. Merge Join  
  
Nested Loops를 자바 코드로 예시를 든다면 중첩 루프를 사용하는 것과 같다.  
```java
for (Student student : students) {
    for (StudentGrade studentGrade : studentGrades) {
        if (student.getId().equals(studentGrade.getStudentId())) { // on절
            tuples.add() ...
        }
    }
}
```
O(N2)으로 레코드가 많을 때 Nested Loop 알고리즘은 효율적이지 않을 것.  
데이터베이스가 실행 계획을 생성할 때 사용자가 원하는 것을 알고 있을 뿐 아니라 데이터베이스가 보유한 데이터도 알아야 한다. 이를 위해 통계를 사용한다.