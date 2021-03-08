---
title: JPA @ManyToOne @OneToMany
category:
  - spring
tags:
  - jpa
---

# JPA  @ManyToOne @OneToMany



```
Member Table
Team Table
```



## @ManyToOne 

- Member-Team

  ```java
  @Entity
  @Table(name="member")
  class Member {
  	...
  	@ManyToOne
  	private Team team;
  }
  ```



```java
@Controller
public MemberController {
    
    @Autowired
    private MemberRepository memberRepository;
    
    @GetMapping("/members")
    @ResponseBody
    public List<Member> getMembers() {
        return memberRepository.findAll();
    }
}
```

조회하면

```
Member 조회
team 조회 where id=team1
team 조회 where id=team2
...
```

- team 개수만큼 더해서 조회됨

- `JpaRepository`에서 제공하는 `method` 들은 기본적으로 전부 `@Transactional` 임

- 따라서 `findAll()` 을 하면 `member`를 가져온 다음에 `member` 가 참조하고 있는 `team`을 가져옴

- 만약, 아래 테스트 코드를 돌리면

  ```java
  @RunWith(SpringRunner.class)
  @DataJpaTest
  public class MemberRepositoryTest {
      @Autowired MemberRepository memberRepository;
      @Autowired TeamRepository teamRepository;
      
      @Test
      public void testSelect() {
          Team team = new Team();
          team.setName("team1");
          Team savedTeam = teamRepository.save(team);
          
          Team team2 = new Team();
          team2.setName("team2");
          Team savedTeam2 = teamRepository.save(team2);
          
          Member member1 = new Member();
          member1.setName("amy");
          member1.setTeam(savedTeam);
          memberRepository.save(member1);
          
          Member memer2 = new Member();
          member2.setName("chris");
          member2.setTeam(savedTeam2);
          memberRepository.save(member2);
          
          memberRepository.findAll().forEach(System.out::println);
      }
  }
  ```

  - `testSelect()` 메서드 내 같은 트랜잭션 안에서 `member` 들이 참조하고 있는 객체가 이미 `save`를 하면서 1차 캐시에 캐싱되어 있기 때문에 `findAll()`을 할 때 다시 불러올 필요가 없으므로 1번만 조회함



### N+1 문제

- Fetch Join 사용

  ```java
  public interface MemberRepository extends JpaRepository<Member, Long> {
      
      @Query("select m from member m join fetch m.team t")
      List<Member> findAll();
  }
  ```

  - `member inner join team on member.team_id=team.id` => inner join
  - `team` 있는 `member` 만 조회됨

- @EntityGraph 사용

  ```java
  @Entity
  @Table(name="member")
  @NamedEntityGraph(name = "MemberWithTeam", attributeNodes = @NamedAttributeNode("team"))
  class Member {
  	...
  	@ManyToOne
  	private Team team;
  }
  ```

  ```java
  public interface MemberRepository extends JpaRepository<Member, Long> {
      
      @EntityGraph("EventWithTeam")
      List<Member> findAll();
  }
  ```

  - `member left outer join team on member.team_id=team.id` => left outer join

  - `team` 없는 `memeber` 도 조회됨





### @OneToMany

- Team-Member

```java
@Entity
@Table(name="team")
class Team {
	...
	@OneToMany(MappedBy="team")
	private Set<Member> members = new HashSet<>();
}
```



- MappedBy = "team"

  - 관계의 주인을 `Member` 클래스의 `team` 으로 넘겨줌

  - 관계의 주인 쪽에 관계가 설정되어야만 데이터베이스에 반영됨

  - 만약, members에 member를 추가하려면

    ```java
    @Entity
    @Table(name="team")
    class Team {
    	...
    	@OneToMany(MappedBy="team")
    	private Set<Member> members = new HashSet<>();
        
        public void add(Member member) {
            members.setTeam(this);	// 여기서 관계 주인한테 관계 설정
            getMembers().add(member);
        }
    }
    ```

    

---

# References

- [JPA @ManyToOne 단방향 관계 쿼리 문제 해설편](https://youtu.be/MpXdx8-qWzo)
- [JPA에 대한 사실과 오해](https://webcoding-start.tistory.com/44)
- [JPA, OneToMany 양방향 관계 "MappedBy" 해설](https://youtu.be/hsSc5epPXDs)



