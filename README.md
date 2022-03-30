# spring-main-point
스프링 실무에 사용 가능한 핵심 내용 정리

<h3 style="font-weight:bold;">slf4j 로그 사용법</h3>
<p>1. 클래스 상단에 @Slf4j 어노테이션 추가</p>
<p>2. log 라는 객체 사용 가능<br>
  log.trace("trace log={}", 변수)<br>
  log.debug("debug log={}", 변수)<br>
  log.info("info log={}", 변수)<br>
  log.info("warn log={}", 변수)<br>
  log.info("error log={}", 변수)
</p>
<p>
 ※주의할 점
  local에서 작업 시에는 trace 나 debug 레벨까지 볼 수 있게 설정하되, 실서버에서는 info 레벨로 설정해야 한다.
  (application.properties 에서 'logging.level.패키지=레벨' 로 설정 가능)
</p>

<h3 style="font-weight:bold;">의존성(의존관계) 주입 방법</h3>
<p>1. 롬복 이용한 방법</p>
<p>
@Component (이 어노테이션을 포함한 어노테이션 ex. @Controller 등도) <br>
@RequiredArgsConstructor <br>
public class serviceImpl implements Service {
  
  private final Repository repository;
  
}
</p>
<p>
 + @RequiredArgsConstructor 는 <br>
  @Autowired <br>
  public serviceImpl(Repository repository){ <br>
    this.repository = repository; <br>
  } <br>
  이렇게 생성자가 1개일 경우에 이 부분을 대신 해주는 어노테이션임!
  
 ※주의할 점 <br>
  @Autowired <br>
  private Repository repository <br>
  식으로 필드주입은 지양해야한다.
</p>
<p>2. 조회한 빈이 모두 필요할 때 (ex. 2가지 정책을 번갈아 써야할 때)</p>
<p>
  public class PublicService {
  
    private final Map<String, Policy> policyMap;
    private final List<Policy> polices;
  
    public PublicService(Map<String, Policy> policyMap, List<Policy> policies) { 
      this.policyMap = policyMap;
      this.policies = policies;
    }
  
    // bean이름으로 가져올 수 있게 메서드 만듦
    public int discount(String discountCode){
      Policy discountPolicy = policyMap.get(discountCode);
      return discountPolicy.discount(...)
    }
  
  }
</p>
<p>
※주의할 점 <br>
등록된 빈이 여러개 일때 <br>
private final Policy policy; <br>
이렇게 하나만 받도록 해버리면 <br>
NoUniqueBeanDefinitionException 오류 발생 <br>
-> 위 처럼 처리하거나 @Qualifier, @Primary 어노테이션 
</p>

<h3 style="font-weight:bold;">스프링 MVC 공통 데이터 처리</h3>
<p>
    @ModelAttribute 의 다른 기능 <br>
    @ModelAttribute("types") <br>
    public Type[] types(){ <br>
      return Type.values(); <br>
    } <br>
(Type : ENUM) <br>
  이렇게 추가해주면 이 컨트롤러의 Model 객체에는 항상 types가 담겨있게 된다.<br>
-> 정말 자주 사용되는 상수 등의 data는 이렇게 처리하자. <br>
(Map이나 LIst등의 타입도 가능한데 객체를 생성해야 돼서 이런식으로 쓰면 비효율 <br>
-> 따로 static으로 만들어놓고 가져오는게 낫다.) <br>
</p>  

<h3 style="font-weight:bold;">Spring에서 지원하는 Validation</h3>   
<p>
    BindingResult(or Error 인터페이스) 을 사용하는 방법, <br>
    view에서 검증 결과를 th:error 로 사용가능하게 된다. <br>
    -> https://github.com/tkdlek0501/Spring-mvc-training-summary <br>
    에서 ProductController와 templates/advanced/addForm 확인 <br>
</p>  
<h3 style="font-weight:bold;">@PathVariable</h3>  
