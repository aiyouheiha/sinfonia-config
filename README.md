# Sinfonia Config

## 1. How to generate a config server

### 1.1. pom

```
...

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>

...

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 1.2. Add @EnableConfigServer on Main Class

```
@EnableConfigServer
@SpringBootApplication
public class ConfigApplication {
	public static void main(String[] args) {
		SpringApplication.run(ConfigApplication.class, args);
	}
}
```

### 1.3. Local Config Server

1. Create application.yml (or .properties)
2. Set content like giving example below

```
spring:
  application:
    name: config
  profiles:
    # !!!
    active: native
  cloud:
    config:
      server:
        native:
          # !!!
          search-locations: classpath:/properties
server:
  port: 20088
```

Run your application, put some profiles under classpath:/properties

Call address like "http://localhost:20088/xxx/default" will get the corresponding profile content.

The suffix of address should follow rules as follows:

```
/{application}/{profile}[/{label}]
/{application}-{profile}.yml
/{label}/{application}-{profile}.yml
/{application}-{profile}.properties
/{label}/{application}-{profile}.properties
```

Whats more:
- xxx.yml is like application.yml, call "xxx/dev" or "xxx/xxx" or whatever, all of these address could get its content.
- Call xxx/dev, once xxx-dev.yml exist will get content, or get a blank content, not error.
- Call /xxx.yml or /xxx.properties with only one of the profile type, will transfer automatic.

Attention:
- When using **local** server, profile of config will be and might only be application.yml (or .properties)

### 1.4. Remote Config Server Based on Git

- application.yml

```
spring:
  application:
    name: config
  profiles:
    active: @profileActive@
```

- application-xxx.yml

```
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/aiyouheiha/sinfonia-config-repo.git
server:
  port: 20088
```

Whats more:
- When using native profiles, label could be set as any value.
- When using git, label must exist.

## 2. How to generate a config client

### 2.1. pom

```
...

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>

...

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 2.2. Create bootstrap.yml (or .properties)

```
spring.cloud.config.uri=http://localhost:20088
spring.application.name=xxx
spring.cloud.config.profile=dev 
spring.cloud.config.label=master
```

Now, application could run without other normal profiles, 
configurations will be got from remote config center.