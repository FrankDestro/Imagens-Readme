# Passos para Preparação de criação e desenvolvimento de projetos (Frontend e Backend)

## Projetos serão desenvolvidos no padrão camadas. 

![camadas](https://github.com/FrankDestro/Project-preparation/blob/main/PADRAO%20CAMADAS.png)

## Checklist :

* Setup inicial do projeto
> 1. Criar o projeto backend no https://start.spring.io/ 
> 1. Validar as Dependências no pom.xml 
> 2. Arquivos.properties - Configurar os arquivos de perfil da aplicação (Criar os arquivos abaixo na pasta resources)

### application.properties

```js
spring.profiles.active=test

spring.jpa.open-in-view=false
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

### application-prod.properties

```html
spring.datasource.url=${DATABASE_URL}
```

### system.properties - Criar na raiz do projeto 

```html
java.runtime.version=17
```

> 3. Configuração de segurança - Criar o arquivo  (SecurityConfig) Vai permitir que o Backend e o Frontend conversem mesmo em ambiente diferentes. 

### Passo: configuração de segurança

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
> 2. Mapeamento Objeto Relacional (ORM - JPA)
> 3. Criar o repositorio (Repository)
> 4. Criar a camada de Serviço (Service)
> 5. Criar o DTO
> 6. Criar o controlador (Controller / REST)

* Criar endpoints na camada controlador REST (de acordo com o projeto )
> 1. [GET]
> 2. [POST]
> 3. [PUT]
> 4. [DELETE] 
> 5. Abir o POSTMAN e criar as requisições para testar. 

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

