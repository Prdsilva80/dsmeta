# Projeto DSMeta

- App de consulta de vendas, no qual você poderá buscar vendas em um intervalo de até um ano atrás, e poderá notificar via SMS os dados dos melhores vendedores.

---

## Tecnologias

<p align="center">
  <a href="https://skillicons.dev">
    <img src="https://skillicons.dev/icons?i=figma,vscode,html,css,nodejs,react,vite,postman,spring,java,netlify" />
  </a>
</p>

---

## Passo: criar projeto ReactJS

- VS Code
  - `IntelliCode`
  - `ESLint`
  - `JSX HTML <tags/>`

## Passo: criar projeto ReactJS

![DevSuperior no Instagram](https://raw.githubusercontent.com/devsuperior/bds-assets/main/sds/pastas-dsmeta.png)

```
yarn create vite frontend --template react-ts
```

## Passo: criar projeto Spring Boot

- Criar projeto Spring Boot no `Spring Initializr` com as seguintes dependências:
  - Web
  - JPA
  - H2
  - Security

- Ajuste no arquivo pom.xml:

```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-resources-plugin</artifactId>
	<version>3.1.0</version><!--$NO-MVN-MAN-VER$ -->
</plugin>
```

- Botão direito no projeto -> Maven -> Update project (force update)


## Passo: Datepicker

Documentação: https://github.com/Hacker0x01/react-datepicker

```
yarn add react-datepicker@4.8.0 @types/react-datepicker@4.4.2
```

```javascript
import DatePicker from "react-datepicker";
import "react-datepicker/dist/react-datepicker.css";
```

```javascript
<DatePicker
    selected={new Date()}
    onChange={(date: Date) => {}}
    className="dsmeta-form-control"
    dateFormat="dd/MM/yyyy"
/>
```


## Passo: React Hook useState para manter estado das datas

Macete para criar uma data de X dias atrás:

```javascript
const date = new Date(new Date().setDate(new Date().getDate() - 365));
```
## Passo: configuração de segurança

```java
import java.util.Arrays;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

	@Bean
	public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
		
		http.headers().frameOptions().disable();
		http.cors().and().csrf().disable();
		http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
		http.authorizeHttpRequests((auth) -> auth.anyRequest().permitAll());

		return http.build();
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

### Passo: banco de dados

- Criar entidade Sale
- Fazer mapeamento objeto-relacional (JPA)
- Configurar dados de conexão do banco de dados H2
- Fazer seed do banco de dados

#### application.properties
```
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```


### Passo: Primeiro teste de endpoint da API REST

- Criar repository
- Criar service
- Criar controller

### Passo: Consulta por data

Consulta customizada:

```java
@Query("SELECT obj FROM Sale obj WHERE obj.date BETWEEN :min AND :max ORDER BY obj.amount DESC")
Page<Sale> findSales(LocalDate min, LocalDate max, Pageable pageable);
```


### Passo: Envio de SMS

Dependências Maven do Twilio
```xml
<dependency>
	<groupId>com.twilio.sdk</groupId>
	<artifactId>twilio</artifactId>
	<version>8.31.1</version>
</dependency>
```

Variáveis de ambiente no application.properties:
```
twilio.sid=${TWILIO_SID}
twilio.key=${TWILIO_KEY}
twilio.phone.from=${TWILIO_PHONE_FROM}
twilio.phone.to=${TWILIO_PHONE_TO}
```

Classe SmsService:
```java
@Service
public class SmsService {

	@Value("${twilio.sid}")
	private String twilioSid;

	@Value("${twilio.key}")
	private String twilioKey;

	@Value("${twilio.phone.from}")
	private String twilioPhoneFrom;

	@Value("${twilio.phone.to}")
	private String twilioPhoneTo;

	public void sendSms() {

		Twilio.init(twilioSid, twilioKey);

		PhoneNumber to = new PhoneNumber(twilioPhoneTo);
		PhoneNumber from = new PhoneNumber(twilioPhoneFrom);

		Message message = Message.creator(to, from, "Teste").create();

		System.out.println(message.getSid());
	}
}
```


Arquivo `system.properties`
```
java.runtime.version=17
```

- Definir variáveis de ambiente:
  - TWILIO_SID
  - TWILIO_KEY
  - TWILIO_PHONE_FROM
  - TWILIO_PHONE_TO
  
  ## Objetivos do projeto
- Integrar back end e front end
- Implantar o front end


## Passo: Primeira requisição com Axios e useEffect

```
yarn add axios@0.27.2
```

## Passo: Listagem de vendas

Definição da BASE_URL:

```javascript
export const BASE_URL = import.meta.env.VITE_BACKEND_URL ?? "http://localhost:8080";
```


## Passo: Passando as datas como argumento


## Passo: Enviar notificação


## Passo: Mensagem Toast de confirmação

```
yarn add react-toastify@9.0.5
```

No App.tsx:
```javascript
import { ToastContainer } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';
```

## Passo: Deploy no Netlify

Antes: acrescente `window.React = React` no seu main.tsx conforme abaixo:

```js
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App'
import './index.css'

window.React = React

ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)
```

https://www.netlify.com/

- Deploy básico
  - Base directory: frontend
  - Build command: yarn build
  - Publish directory: frontend/dist
  - Variáveis de ambiente:
    - VITE_BACKEND_URL

- Configurações adicionais
  - Site settings -> Domain Management: (colocar o nome que você quiser)
  - Deploys -> Trigger deploy
