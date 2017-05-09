# Quickstart for Spring Boot and JSON-RPC for Java

## Server
### Configuration
To get the entire system working, you need to define the AutoJsonRpcServiceImplExporter bean in your @Configuration class:
```Java
package example.jsonrpc4j.springboot;

import com.googlecode.jsonrpc4j.spring.AutoJsonRpcServiceImplExporter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ApplicationConfig {

    @Bean
    public static AutoJsonRpcServiceImplExporter autoJsonRpcServiceImplExporter() {
        AutoJsonRpcServiceImplExporter exp = new AutoJsonRpcServiceImplExporter();
        //in here you can provide custom HTTP status code providers etc. eg:
        //exp.setHttpStatusCodeProvider();
        //exp.setErrorResolver();
        return exp;
    }
}
```

### Service
Then create your service interface. My example is a simple calculator endpoint:
```Java
package example.jsonrpc4j.springboot.api;

import com.googlecode.jsonrpc4j.JsonRpcParam;
import com.googlecode.jsonrpc4j.JsonRpcService;

@JsonRpcService("/calculator")
public interface ExampleServerAPI {
    int multiplier(@JsonRpcParam(value = "a") int a, @JsonRpcParam(value = "b") int b);
}
```
And implement your interface like this:
```Java
package example.jsonrpc4j.springboot.api;

import com.googlecode.jsonrpc4j.spring.AutoJsonRpcServiceImpl;
import org.springframework.stereotype.Service;

@Service
@AutoJsonRpcServiceImpl
public class ExampleServerAPIImpl implements ExampleServerAPI {
    @Override
    public int multiplier(int a, int b) {
        return a * b;
    }
}
```
And you're done! When sending requests to your server, please note that setting the content type header is crucial eg:
```
curl -H "Content-Type:application/json" -d '{"id":"1","jsonrpc":"2.0","method":"multiplier","params":{"a":5,"b":6}}' http://localhost:8080/calculator
```

For windows escape the double quotes
```
curl -H "Content-Type:application/json" -d {\"id\":\"1\",\"jsonrpc\":\"2.0\",\"method\":\"multiplier\",\"params\":{\"a\":5,\"b\":6}} http://localhost:8080/calculator
```
