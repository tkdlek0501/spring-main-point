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
  <br>
  System.out 으로 확인하지 말자. log 또한 사용법이 어렵지 않고 설정 시 콘솔뿐만 아니라 파일로도 남길 수 있다. 
</p>

<h3 style="font-weight:bold;">의존성(의존관계) 주입 방법 (최신 ver)</h3>
<h3 style="font-weight:bold;">@RequiredArgsContructor</h3>
<h3 style="font-weight:bold;">타임리프에서 url 표현식</h3>
<h3 style="font-weight:bold;">@PathVariable</h3>
