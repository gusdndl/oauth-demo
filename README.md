#RestTemplateConfig
RestTemplate은 OAuth2 연결 끊기 기능 구현시 HTTP API 요청을 위해 사용합니다. RestTemplate 객체를 스프링 빈으로 등록하기 위한 설정 클래스입니다.

#SecurityConfig
스프링 시큐리티와 OAuth2 설정 클래스입니다.
CSRF 설정을 Disable 합니다.
OAuth2 핸들러 및 서비스를 빈으로 등록합니다.
#OAuth2AuthenticationProcessingException
OAuth2 인증 관련 실패를 위한 사용자 정의 예외 클래스입니다.

#OAuth2AuthenticationFailureHandler
OAuth2 인증 실패시 호출되는 핸들러입니다. 인증 실패시 처음 프론트엔드에서 백엔드로 로그인 요청시 redirect_uri 쿼리 파라미터에 담긴 주소로 리다이렉트 합니다. 이 때 error 라는 쿼리 파라미터에 오류 메세지를 담아서 리다이렉트합니다.

#OAuth2AuthenticationSuccessHandler
OAuth2 인증 성공시 호출되는 핸들러입니다. 처음 프론트엔드에서 백엔드로 로그인 요청시 mode 쿼리 파라미터에 담긴 값에 따라 분기하여 처리합니다. mode 값이 login 이면 사용자 정보를 DB에 저장하고, 서비스 자체 액세스 토큰, 리프레시 토큰을 생성하고, 리프레시 토큰을 DB에 저장합니다. mode 값이 unlink 이면 각 OAuth2 서비스에 맞는 연결 끊기 API를 호출하고, 사용자 정보를 DB에서 삭제하고, 리프레시 토큰을 DB에서 삭제합니다.

#CustomOAuth2UserService
DefaultOAuth2UserService 클래스를 상속 받아 구현한 사용자 정의 클래스입니다. loadUser 메서드는 스프링 시큐리티 OAuth2LoginAuthenticationFilter 에서 시작된 OAuth2 인증 과정 중에 호출됩니다. 호출되는 시점은 액세스 토큰을 OAuth2 제공자로부터 받았을 때 입니다.
먼저 super.loadUser 를 통해 상위 클래스에 정의된 액세스 토큰으로 사용자 정보를 가져오는 로직을 실행해야합니다.

그 이후 processOAuth2User 메서드를 통해 각 OAuth2 제공자 별 제공되는 사용자 정보를 동일한 인터페이스로 변환하여 리턴합니다.

#OAuth2UserPrincipal
위에서 보았던 processOAuth2User 에서 리턴하는 인증 객체는 OAuth2User 인터페이스 입니다. 이 인터페이스의 사용자 정의 구현체 클래스입니다.

#OAuth2UserInfo
OAuth2 제공자 별로 리턴하는 사용자 정보 데이터의 구조와 필드 이름 등이 다릅니다. 예를 들면 아래는 네이버에서 리턴하는 사용자 정보입니다. 서비스 별로 다른 구조를 통합하기 위한 인터페이스를 정의합니다.

#GoogleOAuth2UserInfo
구글 사용자 정보를 가져와 저장하는 구현체입니다. 아래와 같은 JSON 데이터가 리턴됩니다.

{
    "sub": "....",
    "name": "....",
    "given_name": "....",
    "family_name": "....",
    "picture": "https....",
    "email": "....",
    "email_verified": true,
    "locale": "ko"
}

#NaverOAuth2UserInfo
네이버 사용자 정보를 가져와 저장하는 구현체입니다.

https://developers.naver.com/docs/login/devguide/devguide.md#3-4-5-%EC%A0%91%EA%B7%BC-%ED%86%A0%ED%81%B0%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-%ED%94%84%EB%A1%9C%ED%95%84-api-%ED%98%B8%EC%B6%9C%ED%95%98%EA%B8%B0

#KakaoOAuth2UserInfo
카카오 사용자 정보를 가져와 저장하는 구현체입니다.

https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#req-user-info

#OAuth2UserUnlink
OAuth2 제공자 별로 OAuth2 애플리케이션과 연동 해제 하는 방법이 다릅니다. 서비스 별로 다른 연동 해제 방법을 통합 하기 위한 인터페이스를 정의합니다.

#GoogleOAuth2UserUnlink
구글 OAuth2 연동 해제를 위한 구현체입니다.

#NaverOAuth2UserUnlink
네이버 OAuth2 연동 해제를 위한 구현체입니다.

#KakaoOAuth2UserUnlink
카카오 OAuth2 연동 해제를 위한 구현체입니다.

#OAuth2Provider
OAuth2 서비스를 구분하기 위한 enum 입니다.

#OAuth2UserInfoFactory
OAuth2 인증시 액세스 토큰으로 사용자 정보를 가져왔을 때, OAuth2 제공자 별로 분기하여 OAuth2UserInfo 인터페이스 구현체를 호출하여 OAuth2UserInfo 객체를 생성해주는 팩토리 클래스입니다.

#OAuth2UserUnlinkManager
OAuth2 연동 해제시 OAuth2 제공자 별로 분기하여 OAuth2UserUnlink 인터페이스 구현체를 호출하여 연동 해제해주는 클래스입니다.

#CookieUtils
쿠키에 객체를 직렬화하여 저장하고 역직렬화하여 불러오는 기능을 구현한 클래스입니다. OAuth2 인증 과정에서 프론트엔드에서 백엔드로 최초 요청시 쿼리 파라미터를 인증이 끝날때까지 유지하기 위해 데이터를 쿠키에 직렬화하여 저장합니다. 또한 OAuth2 인증 과정중에 사용하는 state 파라미터 등을 저장합니다.

#HttpCookieOAuth2AuthorizationRequestRepository
OAuth2 인증 과정중에 state, redirect_uri 등의 파라미터를 어딘가에 저장해야하는데 이를 쿠키에 저장하는 방식을 구현한 사용자 정의 클래스입니다. 스프링 빈으로 등록하고 SecurityConfig 에서 authorizationRequestRepository 로 설정합니다.

스프링 시큐리티 OAuth2 관련 필터인 OAuth2AuthorizationRequestRedirectFilter 와 OAuth2LoginAuthenticationFilter 에서 인증 과정중에 호출됩니다.

최초에 프론트엔드에서 로그인 요청시 리다이렉트 할 OAuth2 제공자 별 URL 정보를 쿠키에 저장하여 리다이렉트 합니다. 그 이후 사용자가 로그인 성공시 백엔드로 리다이렉트 될 때 인증 과정 및 사용자 정보 불러오는 과정을 마친 후 쿠키에 저장된 정보를 삭제합니다.

#Oauth2Application
스프링 애플리케이션을 시작하는 메인 클래스입니다.

