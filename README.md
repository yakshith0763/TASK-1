<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>
</dependencies>v

Create a Server class to represent your server objects. This class should include properties like id, name, language, and framework. Annotate it as a MongoDB document:
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document(collection = "servers")
public class Server {
    @Id
    private String id;
    private String name;
    private String language;
    private String framework;

    // Constructors, getters, setters
}
Create a repository interface for MongoDB operations on the Server class:
java
import org.springframework.data.mongodb.repository.MongoRepository;

public interface ServerRepository extends MongoRepository<Server, String> {
    // Custom query methods, if needed
}
Create a controller class to handle REST API endpoints:
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/servers")
public class ServerController {

    @Autowired
    private ServerRepository serverRepository;

    @GetMapping
    public List<Server> getServers(@RequestParam(required = false) String name) {
        if (name != null) {
            return serverRepository.findByNameContaining(name);
        } else {
            return serverRepository.findAll();
        }
    }

    @GetMapping("/{id}")
    public Server getServerById(@PathVariable String id) {
        return serverRepository.findById(id)
                .orElse(null); // Return null if not found
    }

    @PostMapping
    public Server createServer(@RequestBody Server server) {
        return serverRepository.save(server);
    }

    @PutMapping("/{id}")
    public Server updateServer(@PathVariable String id, @RequestBody Server server) {
        server.setId(id); // Ensure the ID is set
        return serverRepository.save(server);
    }

    @DeleteMapping("/{id}")
    public void deleteServer(@PathVariable String id) {
        serverRepository.deleteById(id);
    }
}
Configure your MongoDB connection in the application.properties or application.yml file:
spring.data.mongodb.host=localhost
spring.data.mongodb.port=27017
spring.data.mongodb.database=mydb
