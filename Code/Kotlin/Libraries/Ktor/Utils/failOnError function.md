#code #kotlin #function #ktor 

```kotlin
/**  
 * Throws if the HTTP response status code is an error (>=400). * @return The [HttpResponse] if successful. * @throws ClientRequestException on 4xx error code.  
 * @throws ServerResponseException on 5xx error code.  
 * @throws ResponseException on unknown error code.  
 */
 suspend fun HttpResponse.failOnError() =   
    when (status.value) {  
        in 100..399 -> this  
        in 400..499 -> throw ClientRequestException(this, bodyAsText())  
        in 500..599 -> throw ServerResponseException(this, bodyAsText())  
        else -> throw ResponseException(this, bodyAsText())  
    }
```