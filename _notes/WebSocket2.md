---
tags: dev, java, websocket
---
Created: April 22, 2020 10:21 AM
Last Edited Time: April 22, 2020 10:22 AM
Type: Web

(출처:[http://www.oracle.com/technetwork/articles/Java/jsr356-1937161.html](http://www.oracle.com/technetwork/articles/Java/jsr356-1937161.html))
Java WebSocket(웹소켓) API

1. 웹소켓 정의: 생략
2. 웹소켓 등장배경(소켓, HTTP, 웹소켓의 차이): 생략
3. 웹소켓 개발 필요 조건(Java EE 7 등): 생략
4. 웹소켓 라이프사이클 이벤트(Java 매서드와 매핑)
가. 클라이언트가 HTTP 핸드쉐이크 요청을 보내 연결을 시작한다
나. 서버는 핸드쉐이크 응답을 보낸다
다. 연결이 구성되고, 이제는 HTTP가 아닌 웹소켓으로 통신한다(서버-클라이언트가 대칭상태)
라. 두 피어 모두 메시지를 보내고 받는다
마. 두 피어 중 하나가 연결을 닫는다
5. 웹소켓 프로그래밍
가. 어노테이션 방식
POJO에 @ServerEndpoint 어노테이션을 설정하여 웹소켓 요청을 받는 endpoint로 만든다.
@ServerEndpoint에는 웹소켓 endpoint의 경로를 넣는다.
ex) @ServerEndpoint("/test")
public class TestEndpoint{...}
    
    ```
    위 예제 코드를 보면, "test"라는 상대경로로 endpoint가 지정되는데, 애플리케이션이 배포되면 ws://localhost:8080/root/test와 같은 주소로 접근가능하다.
    경로에는 내부 메서드에서 사용할 수 있는 파라미터를 포함할 수 있다. ex) "/test/{userid}"
    
    웹소켓을 시작하는 POJO(클라이언트)는 @ClientEndpoint 어노테이션을 지정해야한다. 클라이언트 측에서는 요청을 받지 않기 때문에 @ClientEndpoint에 경로를 넣지 않는다.        
    ex) @ClientEndpoint
        public class TestClientEndpoint{...}
        
    웹소켓 연결은 다음과 같은 코드로 수행되며, 이후 @ServerEndpoint나 @ClientEndpoint가 endpoint로 호출된다.
    ex) javax.websocket.WebSocketContainer container = javax.websocket.ContainerProvider.getWebSocketContainer();
        container.connectToServer(TestClientEndpoint.class, new URI("ws://localhost:8080/mytestserver/endpoint"));
        
    웹소켓 연결이 구성되면, Session(Session 인스턴스는 웹소켓이 닫히기 전까지 유효)이 생성되고 @OnOpen 어노테이션이 붙은 endpoint가 호출된다. 
    이 매서드는 다음과 같은 파라미터를 포함할 수 있다.         
    - javax.websocket.Session 파라미터 (생성된 Session)
    - EndpointConfig 인스턴스 (endpoint configuration에 대한 정보를 포함)
    - @PathParam 어노테이션이 붙은 String 파라미터 (0개 이상, endpoint 경로의 파라미터를 참조)
    ex) @OnOpen
        public void testOnOpen (Session session) {
            System.out.println (session.getId());
        }
    
    웹소켓 endpoint가 메시지를 수신하면 @OnMessage 어노테이션이 붙은 매서드가 호출 된다. 해당 매서드는 다음과 같은 파라미터를 포함할 수 있다.
    - javax.websocket.Session 파라미터
    - @PathParam 어노테이션이 붙은 String 파라미터 (0개 이상, endpoint 경로의 파라미터를 참조)
    - 메시지 그 자체
    ex) 송신자에게 메시지를 수신만 하는 경우
        @OnMessage
        public void testOnMessage (String txt){
            System.out.println (txt);
        }
    ex) 송신자에게 리턴 값을 주는 경우
        @OnMessage
        public String testOnMessage (String txt){
            return "I got a msg: "+txt;
        }        
    
    웹소켓을 통해 메시지를 보내는 두번째 방법은, 라이프사이클의 callback 매서드(ex @OnOpen 등의)에서 얻을 수 있는 Session 오브젝트를 활용하는 방법이다.
    Session 인스턴스의 getBasicRemote() 매서드는 WebSocket의 상대 쪽인 RemoteEndpoint를 리턴한다. RemoteEndpoint 인스턴스는 아래와 같이 메시지를 보내는데 사용할 수 있다.
    ex) RemoteEndpoint.Basic remote = session.getBasicRemote();
        remote.sendText("hi there");
    
    웹소켓 연결이 닫힐 때, @OnClose 어노테이션이 붙은 매서드가 호출된다. 해당 매서드는 다음과 같은 파라미터를 포함할 수 있다.
    - javax.websocket.Session 파라미터 (WebSocket이 닫히면 사용 불가. 즉, @OnClose 매서드 리턴 후에는 사용 불가)
    - javax.websocket.CloseReason 파리미터 (웹소켓 종료의 원인이 정상종료인지 프로토콜에러인지 등)
    - @PathParam 어노테이션이 붙은 String 파라미터 (0개 이상, endpoint 경로의 파라미터를 참조)
    ex) @OnClose
        public void testOnClose (CloseReason reason) {
            System.out.println(reason.getReasonPhrase());
        }
    
    만일 에러가 수신되면 @OnError가 붙은 매서드가 호출된다.
    
    ```
    
    나. 인터페이스 방식
    인터페이스 방식은 어노테이션 방식과 다르게 javax.websocket.EndPoint를 상속하고 onOpen, onClose, onError를 오버라이드하여 구현한다.
    ex) public class testEndPoint extends javax.websocket.Endpoint{
    public void opOpen(Session session, EndpointConfig config){...}
    public void opClose(Session session, CloseReason reason){...}
    public void opError(Session session, Throwable throw){...}
    }
    
    ```
    메시지를 다루기 위해서는 다음과 같이 javax.websocket.MessageHandler를 onOpen 매서드에 등록해야한다.
    ex) public void onOpen (Session session, EndpointConfig config){
            session.addMessageHandler (new MessageHandler(){...});
        }
    
    MessageHandler는 두 개의 하위 인터페이스를 가진 인터페이스다. 하나는 MessageHandler.Partial 인터페이스로 메시지의 부분 전송을 알아차리고 싶을때 사용하고
    또 다른 인터페이스인 MessageHandler.Whole은 완전한 메시지의 수신을 알아차리고 싶을 때 사용한다.
    ex) public void onOpen (Session session, EndpointConfig config){
            final RemoteEndpoint.Basic remote = session.getBasicRemote();
            session.addMessageHandler (new MessageHandler.Whole<String>(){
                public void onMessage (String txt){
                    try{
                        remote.sendString ("this is test msg: "+txt);
                    }catch (IOException e){
                        ...
                    }                        
                }
            });                
        }
    
    ```
    
6. 메시지 유형 / 인코더 및 디코더
가. 메시지 타입
- 텍스트 기반 메시지
- 바이너리 메시지
- 퐁 메시지 (웹소켓 연결 자체에 대한 메시지)
    
    나. 메지시 유형 사용
    1) 어노테이션 방식
    각 메시지 유형마다 하나의 @OnMessage 매서드가 허용된다. 메시지 유형에 따라 허용되는 파라미터가 다르다.
    가) 매서드가 텍스트 메시지를 다루는 경우
    - 전체 메시지를 수신하는 String
    - 전체 메시지를 수신하는 Java primitive, class
    - 부분 메시지를 수신하는 String, boolean 쌍
    - blocking 시스템에서 전체 메시지 수신하는 Reader
    - 텍스트 디코더(Decoder.Text 또는 Decoder.TextStream)를 가지고 있는 endpoint를 위한 모든 object 파라미터
    나) 매서드가 바이너리 메시지를 다루는 경우
    - 전체 메시지를 수신하는 byte[] 또는 ByteBuffer
    - 부분 메시지를 수신하는 byte[], boolean 쌍 또는 ByteBuffer, boolean 쌍
    - blocking 시스템에서 전체 메시지를 수진하는 InputStream
    - 바이너리 디코더(Decoder.Binary 또는 Decoder.BinaryStream)를 가지고 있는 endpoint를 위한 모든 object 파라미터
    다) 매서드가 pong 메시지를 다루는 경우
    - pong 메시지 핸들링을 위한 PongMessage
    2) 인터페이스 방식과
    각 세션은 메시지 유형마다 하나의 MessageHandler를 등록할 수 있다.
    
    다. 인코더 및 디코더
    모든 Java 오브젝트는 텍스트 기반 또는 바이너리 메시지로 인코딩될 수 있다.
    메시지는 다른 피어로 전송되며, Java 오브젝트도 다시 디코딩되거나 웹소켓 라이브러리로 인터프리트될 수 있다.
    XML이나 JSON이 웹소켓 메시지로 사용되기도 하는데 인코딩 및 디코딩을 통해 Java 오브젝트를 JSON, XML로 바꾸거나 그 반대를 수행할 수 있다.
    
    ```
    인코더는 javax.websocket.Encoder 인터페이스를 구현하여 정의하고, 디코더는 javax.websocket.Decoder 인터페스를 구현하여 정의한다.
    endpoint 인스턴스는 가능한 인코더 및 디코더가 무엇인지 알아야한다. 
    어노테이션 방식을 사용하면, @ClientEndpoint와 @ServerEndpoint에 있는 인코더 및 디코더 엘리먼트를 통해 인코더 및 디코더 목록이 전달된다.
    아래 코드는 TestJavaObject 인스턴스를 텍스트 메시지로 전환하는 MessageEncoder 클래스를 어떻게 등록하는지 보여준다.
    ex) @ServerEndpoint(value = "/endpoint", encoders = MessageEncoder.class, decoders = Messagedecoder.class)
        public class TestEndpoint {
            ...
        }
        
        class MessageEncoder implements Encoder.Text<TestJavaObject>{
            @Override
            public String encode (TestJavaObject obj) throws EncodingException {
                ...
            }
        }
      
        class MessageDecoder implements Decoder.Text<TestJavaObject>{
            @Override
            public TestJavaObject decode (String src) throws DecodeException {
                ...
            }
            
            @Override 
            public boolean willDecode (String src){
                // 전달 받은 String이 TestJavaObject 인스턴스로 디코딩 되어야 한다면 true 리턴
            }
        }
        
    Encoder 인터페이스는 다음과 같은 하위 인터페이스를 가지고 있다.
    - Encoder.Text (Java 오브젝트를 텍스트 메시지로 변환)
    - Encoder.TextStream (Java 오브젝트를 캐릭터 스트림에 add 함)
    - Encoder.Binary (Java 오브젝트를 바이너리 메시지로 변환)
    - Encoder.BinaryStream (Java 오브젝트를 바이너리 스트림에 add 함)
    
    Decoder 인터페이스는 다음과 같은 하위 인터페이스를 가지고 있다.
    - Decoder.Text (텍스트 메시지를 Java 오브젝트로 변환)
    - Decoder.TextStream (캐릭터 스트림으로부터 Java 오브젝트를 read 함)
    - Decoder.Binary (바이너리 메시지를 Java 오브젝트로 변환)
    - Decoder.BinaryStream (바이너리 스트림으로부터 Java 오브젝트를 read 함)
    ```