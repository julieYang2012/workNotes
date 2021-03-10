JWT 

it contains the MAC(Message Authentication Code)



### JJWT LIB



#### Maven

```

<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.2</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.2</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId> <!-- or jjwt-gson if Gson is preferred -->
    <version>0.11.2</version>
    <scope>runtime</scope>
</dependency>
<!-- Uncomment this next dependency if you are using JDK 10 or earlier and you also want to use 
     RSASSA-PSS (PS256, PS384, PS512) algorithms.  JDK 11 or later does not require it for those algorithms:
<dependency>
    <groupId>org.bouncycastle</groupId>
    <artifactId>bcprov-jdk15on</artifactId>
    <version>1.60</version>
    <scope>runtime</scope>
</dependency>
-->
```

#### Asymmetric Keys

If you want to generate sufficiently strong Elliptic Curve or RSA asymmetric key pairs for use with JWT ECDSA or RSA algorithms, use the `Keys.keyPairFor(SignatureAlgorithm)` helper method:

```
KeyPair keyPair = Keys.keyPairFor(SignatureAlgorithm.RS256); //or RS384, RS512, PS256, PS384, PS512, ES256, ES384, ES512
```

You use the private key (`keyPair.getPrivate()`) to create a JWS and the public key (`keyPair.getPublic()`) to parse/verify a JWS.

**NOTE: The `PS256`, `PS384`, and `PS512` algorithms require JDK 11 or a compatible JCA Provider (like BouncyCastle) in the runtime classpath.** If you are using JDK 10 or earlier and you want to use them, see the [Installation](https://github.com/jwtk/jjwt#Installation) section to see how to enable BouncyCastle. All other algorithms are natively supported by the JDK.



```
If the jws was signed with a SecretKey, the same SecretKey should be specified on the JwtParserBuilder. For example:


String jws = Jwts.builder().setSubject("Joe").signWith(secretKey).compact();
Jwts.parserBuilder()
    
  .setSigningKey(secretKey) // <----
  
  .build()
  .parseClaimsJws(jwsString);
If the jws was signed with a PrivateKey, that key's corresponding PublicKey (not the PrivateKey) should be specified on the JwtParserBuilder. For example:

Jwts.parserBuilder()
    
  .setSigningKey(publicKey) // <---- publicKey, not privateKey
  
  .build()
  .parseClaimsJws(jwsString);
```



```
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.security.Keys;
import java.security.Key;

// We need a signing key, so we'll create one just for this example. Usually
// the key would be read from your application configuration instead.
Key key = Keys.secretKeyFor(SignatureAlgorithm.HS256);

String jws = Jwts.builder().setSubject("Joe").signWith(key).compact();
```

```Standard Claims

```

#### Header Parameters

A JWT Header provides metadata about the contents, format and cryptographic operations relevant to the JWT's Claims.

If you need to set one or more JWT header parameters, such as the `kid` [(Key ID) header parameter](https://tools.ietf.org/html/rfc7515#section-4.1.4), you can simply call `JwtBuilder` `setHeaderParam` one or more times as needed:

```
String jws = Jwts.builder()

    .setHeaderParam("kid", "myKeyId")
    
    // ... etc ...
```

Each time `setHeaderParam` is called, it simply appends the key-value pair to an internal `Header` instance, potentially overwriting any existing identically-named key/value pair.

**NOTE**: You do not need to set the `alg` or `zip` header parameters as JJWT will set them automatically depending on the signature algorithm or compression algorithm used.



##### Header Instance

If you want to specify the entire header at once, you can use the `Jwts.header()` method and build up the header paramters with it:

```
Header header = Jwts.header();

populate(header); //implement me

String jws = Jwts.builder()

    .setHeader(header)
    
    // ... etc ...
```

**NOTE**: Calling `setHeader` will overwrite any existing header name/value pairs with the same names that might have already been set. In all cases however, JJWT will still set (and overwrite) any `alg` and `zip` headers regardless if those are in the specified `header` object or not.



##### Header Map

If you want to specify the entire header at once and you don't want to use `Jwts.header()`, you can use `JwtBuilder` `setHeader(Map)` method instead:

```
Map<String,Object> header = getMyHeaderMap(); //implement me

String jws = Jwts.builder()

    .setHeader(header)
    
    // ... etc ...
```

**NOTE**: Calling `setHeader` will overwrite any existing header name/value pairs with the same names that might have already been set. In all cases however, JJWT will still set (and overwrite) any `alg` and `zip` headers regardless if those are in the specified `header` object or not.

####  Claim

The `JwtBuilder` provides convenient setter methods for standard registered Claim names defined in the JWT specification. They are:

- `setIssuer`: sets the [`iss` (Issuer) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.1)
- `setSubject`: sets the [`sub` (Subject) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.2)
- `setAudience`: sets the [`aud` (Audience) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.3)
- `setExpiration`: sets the [`exp` (Expiration Time) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.4)
- `setNotBefore`: sets the [`nbf` (Not Before) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.5)
- `setIssuedAt`: sets the [`iat` (Issued At) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.6)
- `setId`: sets the [`jti` (JWT ID) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.7)

For example:

```
String jws = Jwts.builder()

    .setIssuer("me")
    .setSubject("Bob")
    .setAudience("you")
    .setExpiration(expiration) //a java.util.Date
    .setNotBefore(notBefore) //a java.util.Date 
    .setIssuedAt(new Date()) // for example, now
    .setId(UUID.randomUUID()) //just an example id
    
    /// ... etc ...
```



##### Custom Claims

If you need to set one or more custom claims that don't match the standard setter method claims shown above, you can simply call `JwtBuilder` `claim` one or more times as needed:

```
String jws = Jwts.builder()

    .claim("hello", "world")
    
    // ... etc ...
```

Each time `claim` is called, it simply appends the key-value pair to an internal `Claims` instance, potentially overwriting any existing identically-named key/value pair.

Obviously, you do not need to call `claim` for any [standard claim name](https://github.com/jwtk/jjwt#jws-create-claims-standard) and it is recommended instead to call the standard respective setter method as this enhances readability.



