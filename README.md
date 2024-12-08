# Aplicativo de Concreto Armado

**Para Engenharia de construção em concreto armado e obras de fundações**

Baseado na norma de concreto armado **NBR 6118/2007**.

## Trechos de código

### Plug-in Maven

```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-resources-plugin</artifactId>
	<version>3.1.0</version> <!--$NO-MVN-MAN-VER$ -->
</plugin>
```

### application.properties

```
spring.profiles.active=${APP_PROFILE:test}
spring.jpa.open-in-view=false

cors.origins=${CORS_ORIGINS:http://localhost:5173,http://localhost:3000}
```

### application-test.properties

```
# H2 Connection
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# H2 Client
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# Show SQL
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

### application-dev.properties

```
#spring.jpa.properties.jakarta.persistence.schema-generation.create-source=metadata
#spring.jpa.properties.jakarta.persistence.schema-generation.scripts.action=create
#spring.jpa.properties.jakarta.persistence.schema-generation.scripts.create-target=create.sql
#spring.jpa.properties.hibernate.hbm2ddl.delimiter=;

spring.datasource.url=jdbc:postgresql://localhost:5432/dscatalog
spring.datasource.username=postgres
spring.datasource.password=1234567

spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none
```

### application-prod.properties
```
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none
```

### system.properties
```
java.runtime.version=17
```

### WebConfig

```java

@Configuration
public class WebConfig {

	@Value("${cors.origins}")
	private String corsOrigins;
	
	@Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/**").allowedMethods("*").allowedOrigins(corsOrigins);
			}
		};
	}
}

```

### ObraRepository

```java

@Query(nativeQuery = true, value = """
		SELECT tb_obra.id, tb_obra.title, tb_obra.img_url AS imgUrl,
		tb_obra.descricao_curta AS descricaoCurta, tb_belonging.position
		FROM tb_obra
		INNER JOIN tb_belonging ON tb_obra.id = tb_belonging.obra_id
		WHERE tb_belonging.list_id = :listId
		ORDER BY tb_belonging.position
			""")
List<ObraMinProjection> searchByList(Long listId);

```

### ObraListRepository

```java
@Modifying
@Query(nativeQuery = true, value = "UPDATE tb_belonging SET position = :newPosition WHERE list_id = :listId AND obra_id = :obraId")
void updateBelongingPosition(Long listId, Long obraId, Integer newPosition);
```

### import.sql

```sql

INSERT INTO tb_obra_list (genero) VALUES ('Condomínio Residencial');
INSERT INTO tb_obra_list (genero) VALUES ('Residencial');

INSERT INTO tb_obra (title, descricao, data_inicio, proprietario, logradouro, cep, bairro, cidade, estado, pais, genero, img_url, descricao_curta, descricao_longa) VALUES ('Águas do Paraíso', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 05/03/2012, 'Rolando Lero', 'Rua Juca Romero, 23', '08440-001', 'Guaianazes', 'São Paulo', 'SP', 'Brasil', 'Condomínio Residencial', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/1.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam. Incidunt dolorum, nisi deleniti dicta odit voluptatem nam provident temporibus reprehenderit blanditiis consectetur tenetur. Dignissimos blanditiis quod corporis iste, aliquid perspiciatis architecto quasi tempore ipsam voluptates ea ad distinctio, sapiente qui, amet quidem culpa.');
INSERT INTO tb_obra (title, descricao, data_inicio, proprietario, logradouro, cep, bairro, cidade, estado, pais, genero, img_url, descricao_curta, descricao_longa) VALUES ('Chuva de Prata', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 24/08/2015, 'Asterix o Gaulês', 'Rua Jacarandá Disposto, 2352', '08440-002', 'Junco Pedroso', 'Itamorandava', 'SP', 'Brasil', 'Residencial', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/2.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam. Incidunt dolorum, nisi deleniti dicta odit voluptatem nam provident temporibus reprehenderit blanditiis consectetur tenetur. Dignissimos blanditiis quod corporis iste, aliquid perspiciatis architecto quasi tempore ipsam voluptates ea ad distinctio, sapiente qui, amet quidem culpa.');
INSERT INTO tb_obra (title, descricao, data_inicio, proprietario, logradouro, cep, bairro, cidade, estado, pais, genero, img_url, descricao_curta, descricao_longa) VALUES ('Trevo de Cássia', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 15/01/2020, 'Michael Night', 'Rua Joachim Vouchet, 1300', '08410-001', 'Socorro', 'Nova Jerusalém', 'ES', 'Brasil', 'Condomínio Residencial', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/3.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam. Incidunt dolorum, nisi deleniti dicta odit voluptatem nam provident temporibus reprehenderit blanditiis consectetur tenetur. Dignissimos blanditiis quod corporis iste, aliquid perspiciatis architecto quasi tempore ipsam voluptates ea ad distinctio, sapiente qui, amet quidem culpa.');
INSERT INTO tb_obra (title, descricao, data_inicio, proprietario, logradouro, cep, bairro, cidade, estado, pais, genero, img_url, descricao_curta, descricao_longa) VALUES ('Palo Alto', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 22/08/2005, 'Obelix o Gaulês', 'Rua Uva e Damascos, 522', '08640-002', 'Saberosa de Jesus', 'Pereira', 'GO', 'Brasil', 'Residencial', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/4.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam. Incidunt dolorum, nisi deleniti dicta odit voluptatem nam provident temporibus reprehenderit blanditiis consectetur tenetur. Dignissimos blanditiis quod corporis iste, aliquid perspiciatis architecto quasi tempore ipsam voluptates ea ad distinctio, sapiente qui, amet quidem culpa.');
INSERT INTO tb_obra (title, descricao, data_inicio, proprietario, logradouro, cep, bairro, cidade, estado, pais, genero, img_url, descricao_curta, descricao_longa) VALUES ('Prenda de Ouro', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 05/03/2015, 'Jusceline Oliveira', 'Rua João de Barro Gari, 230', '09440-001', 'José Firmino', 'São Paulo', 'SP', 'Brasil', 'Condomínio Residencial', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/5.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam. Incidunt dolorum, nisi deleniti dicta odit voluptatem nam provident temporibus reprehenderit blanditiis consectetur tenetur. Dignissimos blanditiis quod corporis iste, aliquid perspiciatis architecto quasi tempore ipsam voluptates ea ad distinctio, sapiente qui, amet quidem culpa.');
INSERT INTO tb_obra (title, descricao, data_inicio, proprietario, logradouro, cep, bairro, cidade, estado, pais, genero, img_url, descricao_curta, descricao_longa) VALUES ('Mirina Firmina', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 04/08/2022, 'Abracurcix o Gaulês', 'Rua Nova Europa, 150', '16741-002', 'Pedro Pedroso', 'Petrere', 'Al', 'Brasil', 'Residencial', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/6.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam. Incidunt dolorum, nisi deleniti dicta odit voluptatem nam provident temporibus reprehenderit blanditiis consectetur tenetur. Dignissimos blanditiis quod corporis iste, aliquid perspiciatis architecto quasi tempore ipsam voluptates ea ad distinctio, sapiente qui, amet quidem culpa.');
INSERT INTO tb_obra (title, descricao, data_inicio, proprietario, logradouro, cep, bairro, cidade, estado, pais, genero, img_url, descricao_curta, descricao_longa) VALUES ('Casas de Mercosul', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 15/11/2020, 'Peter Turgunian', 'Rua Jacinto Figueira Júnior, 300', '95410-101', 'Pirapora Nova', 'Jericó', 'PE', 'Brasil', 'Condomínio Residencial', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/7.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam. Incidunt dolorum, nisi deleniti dicta odit voluptatem nam provident temporibus reprehenderit blanditiis consectetur tenetur. Dignissimos blanditiis quod corporis iste, aliquid perspiciatis architecto quasi tempore ipsam voluptates ea ad distinctio, sapiente qui, amet quidem culpa.');
INSERT INTO tb_obra (title, descricao, data_inicio, proprietario, logradouro, cep, bairro, cidade, estado, pais, genero, img_url, descricao_curta, descricao_longa) VALUES ('Baleia Branca', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 15/12/1995, 'Chatotorix o Bardo', 'Rua Oliveira Telles, 5220', '98640-402', 'Dr. Rossi Mourão', 'Baleia Branca', 'CE', 'Brasil', 'Residencial', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/8.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam. Incidunt dolorum, nisi deleniti dicta odit voluptatem nam provident temporibus reprehenderit blanditiis consectetur tenetur. Dignissimos blanditiis quod corporis iste, aliquid perspiciatis architecto quasi tempore ipsam voluptates ea ad distinctio, sapiente qui, amet quidem culpa.');
INSERT INTO tb_obra (title, descricao, data_inicio, proprietario, logradouro, cep, bairro, cidade, estado, pais, genero, img_url, descricao_curta, descricao_longa) VALUES ('Colinas de Betel', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 15/01/2023, 'Pero Neves', 'Rua Figueira Júnior, 800', '90460-600', 'Januário', '', 'MG', 'Brasil', 'Condomínio Residencial', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/9.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam. Incidunt dolorum, nisi deleniti dicta odit voluptatem nam provident temporibus reprehenderit blanditiis consectetur tenetur. Dignissimos blanditiis quod corporis iste, aliquid perspiciatis architecto quasi tempore ipsam voluptates ea ad distinctio, sapiente qui, amet quidem culpa.');
INSERT INTO tb_obra (title, descricao, data_inicio, proprietario, logradouro, cep, bairro, cidade, estado, pais, genero, img_url, descricao_curta, descricao_longa) VALUES ('Comte Firlan', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 15/12/1974, 'André Bueno', 'Rua Oliveira Silva, 5000', '91640-402', 'Dr. Sami Pitta', 'Natural', 'CE', 'Brasil', 'Residencial', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/10.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam. Incidunt dolorum, nisi deleniti dicta odit voluptatem nam provident temporibus reprehenderit blanditiis consectetur tenetur. Dignissimos blanditiis quod corporis iste, aliquid perspiciatis architecto quasi tempore ipsam voluptates ea ad distinctio, sapiente qui, amet quidem culpa.');

INSERT INTO tb_belonging (list_id, obra_id, position) VALUES (1, 1, 0);
INSERT INTO tb_belonging (list_id, obra_id, position) VALUES (1, 2, 1);
INSERT INTO tb_belonging (list_id, obra_id, position) VALUES (1, 3, 2);
INSERT INTO tb_belonging (list_id, obra_id, position) VALUES (1, 4, 3);
INSERT INTO tb_belonging (list_id, obra_id, position) VALUES (1, 5, 4);

INSERT INTO tb_belonging (list_id, obra_id, position) VALUES (2, 6, 0);
INSERT INTO tb_belonging (list_id, obra_id, position) VALUES (2, 7, 1);
INSERT INTO tb_belonging (list_id, obra_id, position) VALUES (2, 8, 2);
INSERT INTO tb_belonging (list_id, obra_id, position) VALUES (2, 9, 3);
INSERT INTO tb_belonging (list_id, obra_id, position) VALUES (2, 10, 4);
```
