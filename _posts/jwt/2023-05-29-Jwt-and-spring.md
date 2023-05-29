---
title: JWT token and Spring boot application
layout: post
tags:
- Java
- Spring
- JWT

categories:
- JWT

---

### What is JWT token?

JWT token is a standard which allows us to transfer JSON objects between applications securely. 
It is no a way to encrypt the information in order to prevent steeling it. Anybody can decrypt your token if it has it. You can try to decrypt the token at [jwt.io](https://jwt.io). We can usually use the JWT token in an authorization process or for exchanging information between applications. 


### JWT token structure?

A JWT token consist of three parts: header, payload and signature. All this three parts are joined with ".". For example: HEADER.PAYLOAD.SIGNATURE. More about the JWT token you can read [jwt.io](https://jwt.io/introduction).

If we take for example the token: eyJhbGciOiJIUzUxMiJ9.eyJ0b2tlbkRhdGEiOnsiaWQiOiJ0ZXN0SWQiLCJ0eXBlVXNlciI6InByaXZhdGVfdXNlciJ9LCJleHAiOjE2ODUxODg1NzAsImlhdCI6MTY4NTE4NDk3MH0.N6bPuNucfK2-sS1FmRdjWzibc4Ms37Bh-P4C79ARAL8V5-j6M4axkK4d8lQG-8on5WdQpFnUU6BuJ0qKBnqIcw

We can see that there are 3 parts of this token:

1. HEADER: eyJhbGciOiJIUzUxMiJ9
2. PAYLOAD: eyJ0b2tlbkRhdGEiOnsiaWQiOiJ0ZXN0SWQiLCJ0eXBlVXNlciI6InByaXZhdGVfdXNlciJ9LCJleHAiOjE2ODUxODg1NzAsImlhdCI6MTY4NTE4NDk3MH0
3. SIGNATURE: N6bPuNucfK2-sS1FmRdjWzibc4Ms37Bh-P4C79ARAL8V5-j6M4axkK4d8lQG-8on5WdQpFnUU6BuJ0qKBnqIcw 

Below I am showing you how you can encode/decode a JWT token in a spring project. I suppose that you are familiar with maven, java, spring and rest services.

### Generate a JWT token in a spring application.


In a java application is very easy to encode a JWT token or to decoded. You can use the libraries below which provides you everything:

```java

         <dependency>
			<groupId>io.jsonwebtoken</groupId>
			<artifactId>jjwt-api</artifactId>
			<version>0.11.5</version>
		</dependency>
		<dependency>
			<groupId>io.jsonwebtoken</groupId>
			<artifactId>jjwt-impl</artifactId>
			<version>0.11.5</version>
		</dependency>
		
```

After you have have added your dependencies to the project you need to define a key which is at least 512 bytes in order to generate your jwt token. 
In this small project I used spring-boot so I defined my key in the application.yml file of my application. The next step that we need to do is to create a class,
I created a Service in spring where I wrote all my logic relating to generating the token and decoding it.

First I need to generate the token so I implement the method *generateJwtToken*. In my case the method takes any object and an integer seconds. The method writes the data in the token and sets the validity of the token. (I calculate the validity from the current date plus the seconds that we get as a parameter)
I implemented the method *getSigningKey* which provides me the key to sign the JWT token. See below the java code.

```java
 public String generateJwtToken(Object tokenData, int validityInSeconds) {
    Date date = new Date();
    Date expireDate = new Date(date.getTime() + validityInSeconds*1000);
    Map<String, Object> claims =new HashMap<>();
    claims.put(TOKEN_DATA, tokenData);


    return Jwts.builder().setClaims(claims)
        .setIssuedAt(date).setExpiration(expireDate)
        .signWith(getSigningKey()).compact();
  }

  private Key getSigningKey(){
    byte[] secret = Base64.getEncoder().encode(applicationProperties.getJwtSecret().getBytes());
    return new SecretKeySpec(secret, SignatureAlgorithm.HS512.getJcaName());
  }

```
Next we need to implement the method to decode the JWT token. Before decoding it we check if the token is not expired. The jsonwebtoken library provide us a parser that we can initialize with the signing key. Below you can see the decode method.
 
```java
public <T> T decodeJwtToken(String token, Class<T> requiredType) {

    if(isTokenExpired(token)){
      return null;
    }
    Object data = getJwtParser().parseClaimsJws(token).getBody().get(TOKEN_DATA);
    ObjectMapper mapper = new ObjectMapper();
    return mapper.convertValue(data,requiredType);
  }
  private Boolean isTokenExpired(String token) {
    final Date expiration = getExpirationDateFromToken(token);
    return expiration.before(new Date());
  }

  private Date getExpirationDateFromToken(String token) {
    return getClaimFromToken(token, Claims::getExpiration);
  }

  private <T> T getClaimFromToken(String token, Function<Claims, T> claimsResolver) {
    final Claims claims = getJwtParser().parseClaimsJws(token).getBody();
    return claimsResolver.apply(claims);
  }

  private JwtParser getJwtParser(){
    if(jwtParser == null){
      jwtParser = Jwts.parserBuilder().setSigningKey(getSigningKey()).build();
    }
    return jwtParser;
  }

```
Now we just need to test the service. For that I implemented a controller which provides two services one to encode the data and one to decode as you can see below.

```java

@RestController
@RequestMapping(path="/api/jwt")
public class JwtController {

	private final JWTTokenService jwtTokenComponent;

	public JwtController(JWTTokenService jwtTokenComponent) {
		this.jwtTokenComponent = jwtTokenComponent;
	}

	@GetMapping("/generateToken")
	public String generateJwtToken() {
		TokenInfo tokenInfo = new TokenInfo("testId","private_user");
		return jwtTokenComponent.generateJwtToken(tokenInfo,3600);
	}

	@PostMapping("/decodeToken")
	public ResponseEntity generateJwtToken(@RequestBody String jwtToken) {
		TokenInfo tokenInfo = jwtTokenComponent.decodeJwtToken(jwtToken,TokenInfo.class);
		return ResponseEntity.ok(tokenInfo);
	}

}

```

The code that I wrote it for this article you can see it here [jwt-with-spring](https://github.com/frincuandreea/blog-projects-examples/tree/main/jwt-with-spring).
See you next time!
   