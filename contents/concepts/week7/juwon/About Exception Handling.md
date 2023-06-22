# About Exception Handling

## Error vs Exception

���ܿ� ������ ���̴� �����ΰ�?  
����(Exception) : �Է� ���� ó���� �Ұ����ϰų� ������ ���� �߸��� ��� �� **���ø����̼��� ���������� �������� ���ϴ� ��Ȳ**  
-> �����ڰ� ���� ó���� �� �����Ƿ� �̸� �ڵ� ���踦 ���� ó��!

����(Error) : Spring������ JVM���� �߻���Ű�� ������ **���ܿ� �޸� ���ø����̼� �ڵ忡�� ó���� �� �ִ� ���� ���� ����**  
-> �޸� ����(OutOfMemory), ���� �����÷ο�(StackOverFlow) ���� ����  
-> �̸� ���ø����̼� �ڵ带 ���캸�鼭 ������ �߻����� �ʵ��� �����ؼ� ��õ������ �����ؾ� ��!

## Exception Class

![���� Ŭ������ ��� ����](./img/exception_fig1.png)

��� ���� Ŭ������ Throwable Ŭ������ ��ӹް�,  
Exception Ŭ������ Checked Exception�� Unchecked Exception���� ����

Checked Exception : �ݵ�� ���� ó���� �ʿ��� ���ܷ�, ������ �ܰ迡�� Ȯ���� ������ ����, (IOException, SQLException...)  
Unchecked Exception : ����ó���� ���������� �ʴ� ���ܷ�, ��Ÿ�� �ܰ迡�� Ȯ���� ������ ���� (RuntimeException, NullPointerException, IllegalArgumentException...)

-> �����ϰ� ���ڸ� RuntimeException�� ��ӹ޴� Exception class = Unchecked Exception, �ƴϸ� Checked Exception!

## Exception Handling Methods

���� ó���� ����� �� ������ �����մϴ�

- ���� ����

���� ��Ȳ�� �ľ��ؼ� ������ �ذ�! (try/catch ��)

- ���� ó�� ȸ��

���ܰ� �߻��� �������� �ٷ� ó���ϴ� ���� �ƴ� ���ܰ� �߻��� �޼��带 ȣ���ϴ� ������ ���� ó���� �� �� �ְ� ���� (throw ��)

- ���� ��ȯ

���� ���� + ���� ó�� ȸ��  

## Exception Handling in Spring boot

�� ���� ���ø����̼ǿ����� �ܺο��� ������ ��û�� ��� �����͸� ó���ϴ� ��찡 ����  
�� �������� ���� �߻� �� ���� ������ ����� �ƴ� Ŭ���̾�Ʈ�� � ������ �߻��ߴ� �� ��Ȳ�� �����ϴ� ��찡 �Ϲ���  

���ܰ� �߻����� ��, Ŭ���̾�Ʈ�� ���� �޽����� �����Ϸ��� �� Layer���� �߻��� ���ܸ� ��������Ʈ ������ ��Ʈ�ѷ��� �����ؾ� ��  
�̷��� ���޹��� ���ܸ� ó���ϴ� ����� ũ�� �� ������ �ֽ��ϴ�

- @(Rest)ControllerAdvice�� @ExceptionHandler�� ���� ��� ��Ʈ�ѷ��� ���� ó��
- @ExceptionHandler�� ���� Ư�� ��Ʈ�ѷ��� ���ܸ� ó��

-> @ControllerAdvice vs @RestControllerAdvice? (@ControllerAdvice + @ResponseBody !)

### @RestControllerAdvice, @ExceptionHandler

```java
@RestControllerAdvice
public class CustomExceptionHandler {
	
	@ExceptionHandler(value = RuntimeException.class)
	public ResponseEntity<Map<String, String>> handleException(RuntimeException e, HttpServletRequest request) {
		HttpHeaders responseHeaders = new HttpHeaders();
		HttpStatus httpStatus = HttpStatus.BAD_REQUEST;
		
		Map<String, String> map = new HashMap<>();
		map.put("Error Type", httpStatus.getReasonPhrase());
		map.put("code", "400");
		map.put("message", e.getMessage());
		
		return new ResponseEntity<>(map, responseHeaders, httpStatus);
	}
	
}
```

���� @RestControllerAdvice���� ������ �����ϴ� ��� Ư�� ��Ű�������� ���ܸ� ����  
-> @RestControllerAdvice(basePackages = "com.springboot.valid_exception")

@(Rest)ControllerAdvice ���� �켱������?  
-> @(Rest)ControllerAdvice���� @ExceptionHandler�� ����ó���� �����ϴ� ��� �� �ڼ��� Exception�� �켱 (RuntimeException ���� NullPointerException�� �켱���� ����)

### @ExceptionHandler in Controller

```java
@RestController
public class ExceptionController {
	
	@GetMapping
	public void getRuntimeException() {
		throw new RuntimeException("getRuntimeException �޼ҵ� ȣ��");
	}
	
	@ExceptionHandler(value = RuntimeException.class)
	public ResponseEntity<Map<String, String>> handleException(RuntimeException e, HttpServletRequest request) {
		HttpHeaders responseHeaders = new HttpHeaders();
		responseHeaders.setContentType(MediaType.APPLICATION_JSON);
		HttpStatus httpStatus = HttpStatus.BAD_REQUEST;
		
		Map<String, String> map = new HashMap<>();
		map.put("Error Type", httpStatus.getReasonPhrase());
		map.put("code", "400");
		map.put("message", e.getMessage());
		
		return new ResponseEntity<>(map, responseHeaders, httpStatus);
	}
	
}
```

��ó�� �����ϴ� ��� �ش� Ŭ������ �����ؼ� ����ó���� ������ �� ����  
 
Controller ������ @ExceptionHandler�� ����ó���� �ϴ� ���ÿ� @(Rest)ControllerAdvice�� ����ó���� �Ѵٸ�?  
-> ������ ���� Controller�� �ڵ鷯 �޼��尡 �켱������ ����

## CustomException

Ŀ���� ���ܸ� ����ϴ� ����? ���ֿ̹� �������� �ǵ��� ���� �� ����!  

### How to implement CustomException?

���ܰ� �߻��ϴ� ��Ȳ�� �ش��ϴ� ���� ���� Ŭ������ ��ӹ޾Ƽ� ����
CustomException(HttpStatus, message) ���� ���·� ���ؼ� ���� ���� Ŭ���� ����ϴ� ���!