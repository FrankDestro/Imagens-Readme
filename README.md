# Passos para Preparação de criação e desenvolvimento de projetos (Backend)

## Projetos serão desenvolvidos no padrão camadas. 

![camadas](https://github.com/FrankDestro/Project-preparation/blob/main/PADRAO%20CAMADAS.png)

## IMPLANTAÇÃO DO BACKEND DO PROJETO 
### Checklist :

* Setup inicial do projeto
> 1. Criar o projeto backend no https://start.spring.io/ 
> 1. Validar as Dependências no pom.xml 
> 2. Arquivos.properties - Configurar os arquivos de perfil da aplicação (Criar os arquivos abaixo na pasta resources)

### Arquivo pom.xml 

```js
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.4</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.management</groupId>
    <artifactId>project_managment</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>project_managment</name>
    <description>Project management system</description>
    <properties>
        <java.version>11</java.version>
        <spring-cloud.version>Hoxton.SR8</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework.security.oauth.boot</groupId>
            <artifactId>spring-security-oauth2-autoconfigure</artifactId>
        </dependency>
        <dependency>
            <groupId>org.jetbrains</groupId>
            <artifactId>annotations</artifactId>
            <version>RELEASE</version>
            <scope>compile</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

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
</project>
```

### application.properties

```js
spring.profiles.active=test

spring.jpa.open-in-view=false
```
```js
spring.profiles.active=${APP_PROFILE:test}

spring.jpa.open-in-view=false

security.oauth2.client.client-id=${CLIENT_ID:dscatalog}
security.oauth2.client.client-secret=${CLIENT_SECRET:dscatalog123}

jwt.secret=${JWT_SECRET:MY-JWT-SECRET}
jwt.duration=${JWT_DURATION:86400}
```

### application-test.properties

```html
# Dados de conexão com o banco H2
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# Configuração do cliente web do banco H2
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# Configuração para mostrar o SQL no console
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

### application-dev.properties

```html
#spring.jpa.properties.javax.persistence.schema-generation.create-source=metadata
#spring.jpa.properties.javax.persistence.schema-generation.scripts.action=create
#spring.jpa.properties.javax.persistence.schema-generation.scripts.create-target=create.sql
#spring.jpa.properties.hibernate.hbm2ddl.delimiter=;

spring.datasource.url=jdbc:postgresql://localhost:5432/dsmovie
spring.datasource.username=postgres
spring.datasource.password=1234567

spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none
```
```js
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
```

### application-prod.properties

```html
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none
```

### system.properties - Criar na raiz do projeto 

```html
java.runtime.version=17
```

> 3. Configuração de segurança - Criar o arquivo  (SecurityConfig) Vai permitir que o Backend e o Frontend conversem mesmo em ambiente diferentes. 

### Passo: configuração de segurança

### Arquivo de Liberação dos Endpoints (h2, postman etc.)


```js

import java.util.Arrays;

import org.jetbrains.annotations.NotNull;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.Environment;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;



    @Configuration
    @EnableWebSecurity
    public class SecurityConfig extends WebSecurityConfigurerAdapter {

        @Override
        public void configure(@NotNull WebSecurity web) throws Exception {
            web.ignoring().antMatchers("/**");
        }
    }

```
### Arquivo 2 de Liberação dos Endpoints (h2, postman etc.)

```js

import java.util.Arrays;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.Environment;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;


@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Autowired
	private Environment env;

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		if (Arrays.asList(env.getActiveProfiles()).contains("test")) {
			http.headers().frameOptions().disable();
		}
		
		http.cors().and().csrf().disable();
		http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
		http.authorizeRequests().anyRequest().permitAll();
	}

	@Bean
	CorsConfigurationSource corsConfigurationSource() {
		CorsConfiguration configuration = new CorsConfiguration().applyPermitDefaultValues();
		configuration.setAllowedMethods(Arrays.asList("POST", "GET", "PUT", "DELETE", "OPTIONS"));
		final UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
		source.registerCorsConfiguration("/**", configuration);
		return source;
	}
}
```

* Modelo de domínio e camadas da aplicação do banco de dados até as requisições. 
> 1. Entities 
> 2. Mapeamento Objeto Relacional (ORM - JPA) e Mapear as ASSOCIAÇÕES 
> 3. Seeding do Banco de Dados
> 4. Criar o repositorio (Repository)
> 5. Criar a camada de Serviço (Service)
> 6. Criar o DTO
> 7. Criar o controlador (Controller / REST)

* Criar no SERVICE (As operações com Banco de Dados) e endpoints na camada controlador REST (de acordo com o projeto)
> 1. [GET]
> 2. [POST]
> 3. [PUT]
> 4. [DELETE] 
> 5. Abir o POSTMAN e criar as requisições para testar. 


## IMPLANTAÇÃO DA SEGURANÇA DO PROJETO (BeanValidation, Spring Security, Oauth e etc.)

![security1](https://github.com/FrankDestro/Project-preparation/blob/main/Slide1.PNG)

![security2](https://github.com/FrankDestro/Project-preparation/blob/main/Slide2.PNG)

## IMPLANTAÇÃO DO BACKEND NO HEROKU 
> 1. Criar app no Heroku
> 2. Provisionar banco Postgres
> 3. Definir variável APP_PROFILE=prod
> 4. Conectar ao banco via pgAdmin
> 5. Criar seed do banco

### Comandos para associar o projeto local ao APP criado no HEROKU. 
```js
heroku -v
heroku login
heroku git:remote -a <nome-do-app>
git remote -v
git subtree push --prefix backend heroku main
```
## Arquivo DockerFile para gerar um imagem. 
```js
FROM openjdk:11
VOLUME /tmp
EXPOSE 8080
ADD ./target/dscatalog-0.0.1-SNAPSHOT.jar dscatalog.jar
ENTRYPOINT ["java","-jar","/dscatalog.jar"]
```
