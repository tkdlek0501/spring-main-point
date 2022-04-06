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
  이렇게 생성자가 1개일 경우에 이 부분을 대신 해주는 어노테이션 (final키워드를 사용한 인스턴스들만 Bean 객체로 등록)
  
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
    1. 컨트롤러의 메서드에서 매개변수로 객체 받아올 때 @ModelAttribute 사용시 <br>
    ex. @Validated @ModelAtrribute("product") Product product <br>
    로 @Validated 어노테이션 추가 -> 검증 관련 지원 <br>
    <br>
    2. 검증을 해당 @ModelAttribute 로 받은 객체에 어노테이션을 이용해서 추가 <br>
    ex. Product 클래스에서 <br>
    @NotBlank <br>
    private int field <br>
    ※ 각 필드 검증은 위처럼 하되, 객체 이상 단위로 검증시에는 직접 BindingResult에 Error 추가하는 것이 유연하게 사용가능 <br>
    ex. <br>
    if(조건) { <br>
				bindingResult.reject(code, args, defaultMessage); <br>
		} <br>
    <br>
    3. 에러가 있을 때 처리 로직 <br>
    if(bindingResult.hasErrors()){ <br>
      여기서 처리 <br>
    } <br>
    <br>
    4. view 템플릿에서 errors 사용하는 방법 (model에 안담아도 됨) <br>
    1) form 태그 th:object="${product}" 에 @ModelAttribute 이름과 맞추고 <br>
    2) th:errors="*{productName}" 를 통해 해당 필드 오류가 있을 때 오류메시지 출력 <br>
    3) 글로벌 오류 처리는 <br>
     &lt;div th:if="${#fields.hasGlobalErrors()}"&gt; <br>
 		  &lt;p th:each="err : ${#fields.globalErrors()}" th:text="${err}">글로벌 오류 메시지&lt;/p&gt; <br>
     &lt;/div&gt; <br>
     <br>
    자세히 : https://github.com/tkdlek0501/Spring-mvc-training-summary <br>
    에서 validation 패키지 확인 <br>
</p>
<h3 style="font-weight:bold;">인터셉터 구현 및 등록</h3>
: 로그인 했는지 안했는지 등 구분을 위한 인터셉터 <br>
1. HandlerInterceptor 를 상속받은 클래스를 만든다. <br>
2. preHandle 메서드를 오버라이드해서 구현해준다. (handler 호출 전 인터셉트 로직) <br>
ex) <br>
public class MyInterceptor implements HandlerInterceptor { <br>	
@Override <br>
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws IOException { <br>

	HttpSession session = request.getSession();
	if(session == null || session.getAttribute(...) == null){
		response.sendRedirect("/");
		return false;
	}
	return true;	
} <br>
3. WebMvcConfigurer을 상속받은 WebConfig 에 addInterceptors 구현 (인터셉터 등록) <br>
ex) <br>
@Override <br>
public void addInterceptors(InterceptorRegistry registry){ <br>
	registry.addInterceptor(new MyInterceptor()) <br>
	.order(1) <br>
	.addPathPatterns("/**") <br>
	.excludePathPatterns("/login", "...", ...); <br>
} <br>
	
<h3 style="font-weight:bold;">에러페이지 설정</h3>
1) 뷰 템플릿 <br>
'templates/error/' 경로에 4xx.html 등의 HTTP 상태코드 이름으로 html 파일 추가 <br>
2) 정적 리소스 <br>
'static/error/' 경로에 위와 같이 파일 추가 <br>
3) 적용 대상 없으면 'templates/error.html' 이 기본 오류 페이지 <br>

<h3 style="font-weight:bold;">API 설계 (ResponseBody 사용법)</h3> 
<a href="https://github.com/tkdlek0501/Spring-mvc1-part2-summary/blob/main/src/main/java/hello/springmvc/basic/response/ResponseBodyController.java">MVC 기본 기능 - responseBody</a> 	

<h3 style="font-weight:bold;">API 예외 처리 (@RestControllerAdvice, @ExceptionHandler)</h3>  	
@RestControllerAdvice(annotation = RestController.class) // @RestController가 붙는 컨트롤러만 적용. 이 부분을 특정 패키지 or 컨트롤러로 설정해도 됨 (생략시 모든 컨트롤러에 적용)<br> 
public class ExControllerAdvice {

	@ResponseStatus(HttpStatus.BAD_REQUEST)	
	@ExceptionHandler(IllegalArgumentException.class)
	public ErrorResult illegalExHandler(IllegalArgumentException e){
		return new ErrorResult("...", e.getMessage());
	}	
}	
	
<h3 style="font-weight:bold;">타입 컨버터</h3>	
<p>컨버터와 포맷터 사용하는 방법</p>
<p>1. 등록</p>	
	1) 스프링 빈으로 등록</br>
ex) WebConfig에서 addFormatters 메서드 오버라이드<br>

@Override <br>
public void addFormatters(FormatterRegistry registry) { <br>
	// 컨버터 등록 <br>
	registry.addConverter(new ...Converter()); <br>
	// 포멧터 등록 <br>
	registry.addFormatter(new ...Formatter()); <br>
} <br><br>
2) 인스턴스 생성 후 addConverter 메서드로 등록</br>	
ex)<br>
DefaultFormattingConversionService conversionService = new DefaultFormattingConversionService(); <br>
// 컨버터 등록 <br>
conversionService.addConverter(new ...Converter()); <br>
// 포멧터 등록 <br>
conversionService.addFormatter(new ...Converter()); <br>	
<br>
<p>2. 사용</p>	
	1) 스프링 빈으로 등록시 @RequestParam, @ModelAttribute 어노테이션이 자동으로 컨버팅
타임리프에서도 자동 컨버팅 하려면 '${{...}}' 이용하면 된다.<br>
	<br>
	2) convert 메서드 사용<br>	
	ex) 숫자 형식을 문자 형식으로 변환<br>
	conversionService.convert(1000, String.class)<br>
<br>
<p>3. 스프링이 제공하는 기본 포맷터</p>	
@NumberFormat : 숫자 관련 형식 지정 <br>
@DateTimeFormat : 날짜 관련 형식 지정 <br>
ex) <br>
@Data <br>
	public class Dto { <br>
		@NumberFormat(pattern = "###,###") <br>
		private Integer number; <br>
		@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") <br>
		private LocalDateTime localDateTime; <br>
	} <br>
<p>주의: 타입 컨버팅은 @RequestParam, @ModelAttribute 등에서 자동으로 등록된 컨버터로 처리해준다.</p>	
<br>	
	
<p>자세히: https://github.com/tkdlek0501/Spring-mvc-training-summary/tree/main/src/main/java/hello/itemservice/converter</p>

	
	
<h3 style="font-weight:bold;">message 사용 방법</h3>  	
<h3 style="font-weight:bold;">@PathVariable</h3>  
<h3 style="font-weight:bold;">@SessionAttribute</h3>  	
