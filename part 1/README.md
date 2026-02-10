# Зависимости в maven и их значение

### @SpringBootApplication
Аннотация второго класса — @SpringBootApplication. Эта аннотация известна как мета-аннотация, она объединяет @SpringBootConfiguration, @EnableAutoConfiguration и @ComponentScan.
Это "включатель" всего Spring Boot приложения!

### @EnableAutoConfiguration?
Это аннотация, которая включает автоматическую конфигурацию Spring Boot. Она анализирует classpath и автоматически настраивает бины, которые вы, вероятно, понадобятся.

_Стартеры и автоконфигурация
Автоматическая конфигурация разработана так, чтобы хорошо работать со стартерами, но эти две концепции не связаны напрямую. Вы свободны выбирать зависимости от банки вне стартовых. Spring Boot по-прежнему старается максимально настраивать ваше приложение._

### SpringApplication.run():
Это главный метод запуска Spring Boot приложения. Давайте разберем его по частям:

Этапы запуска:
Инициализация Spring контекста
Сканирование компонентов (классы с @Component, @Service, @Repository, @Controller)
Автоконфигурация на основе зависимостей
Запуск встроенного веб-сервера (Tomcat/Jetty/Undertow)
Запуск CommandLineRunner/ApplicationRunner бинов
### специальный стартер, который даёт полезные стандартные характеристики Maven. сама по себе не обеспечивает никаких зависимостей.
```
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>4.0.2</version>
	</parent>
```
### для веб приложений
```
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>
```
**1. @RequestMapping - задает маршрут (URL)**
@RequestMapping("/")
Сопоставляет HTTP запросы с определенным URL путем
В вашем примере: все запросы к корневому пути / будут обрабатываться методом

**2. @RestController - контроллер REST API**
@RestController
Объединяет @Controller + @ResponseBody
Возвращаемые значения методов автоматически конвертируются в JSON/XML
Не требует шаблонов представлений (как @Controller)
<img width="962" height="157" alt="image" src="https://github.com/user-attachments/assets/3e259a30-70a1-48b7-9c95-4df031972c7d" />
REST Controller (REST-контроллер) — это контроллер в Spring, предназначенный для создания RESTful Web Services (веб-сервисов, которые следуют архитектурному стилю REST).
<img width="962" height="157" alt="image" src="https://github.com/user-attachments/assets/ddde67b0-422f-4141-9389-424d09c41bda" />


_Аннотации @RestController и @RequestMapping — это аннотации Spring MVC (они не специфичны для Spring Boot)_

## для развертывания и распостранения 
это одна из привелегий spring boot
```
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

**Starter** = предварительно настроенный набор зависимостей для конкретной задачи.
[стартеры для Spring boot]([url](https://docs.spring.io/spring-boot/reference/using/build-systems.html#:~:text=%D0%A2%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D0%B0%201.%20%D0%A1%D1%82%D0%B0%D1%80%D1%82%D0%B5%D1%80%D1%8B%20%D0%B4%D0%BB%D1%8F%20%D0%BF%D1%80%D0%B8%D0%BB%D0%BE%D0%B6%D0%B5%D0%BD%D0%B8%D0%B9%20Spring%20Boot))

_можно нажать в редакторе POM `ctrl-space` и ввести «spring-boot-starter» для полного списка._

### Используйте @Import когда:
java
// 1. Нужен явный контроль над порядком
@Configuration
@Import({ // Порядок важен!
    PrimaryConfig.class,      // Сначала
    SecondaryConfig.class,    // Потом
    TertiaryConfig.class      // В конце
})

// 2. Конфигурации в разных пакетах
@Configuration
@Import({
    com.example.db.DatabaseConfig.class,
    com.example.security.AuthConfig.class,
    com.example.messaging.QueueConfig.class
})

// 3. Условные импорты
@Configuration
@ConditionalOnProperty(name = "feature.redis.enabled", havingValue = "true")
@Import(RedisConfig.class)
public class FeatureConfig { ... }

**без него в SpringBootApplication встроен ComponentScan, который сам проверяет корневую папку и подпапки**

_Нужно добавлять только одну @SpringBootApplication или @EnableAutoConfiguration аннотации. Обычно мы рекомендуем добавлять один из них только в основной @Configuration курс._
В любой момент вы можете начать определять собственную конфигурацию, чтобы заменить определённые части автонастройки.

#### Если вы обнаружили, что применяются конкретные классы автонастройки, которые вам не нужны, вы можете использовать атрибут exclude @SpringBootApplication для их отключения, как показано в следующем примере:
```
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.jdbc.autoconfigure.DataSourceAutoConfiguration;

@SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })
public class MyApplication {

}
```

```
// Если класс ЕСТЬ в classpath (у вас подключена зависимость)
@SpringBootApplication(exclude = {
    DataSourceAutoConfiguration.class,      // Исключаем по классу
    SecurityAutoConfiguration.class         // Класс доступен в classpath
})
public class Application { ... }

// Если класса НЕТ в classpath (зависимость не подключена)
@SpringBootApplication(excludeName = {
    "org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration",
    "org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration"
    // Указываем полное имя класса как строку
})
public class Application { ... }
```

### @Service
Что делает:
Помечает класс как Spring Bean
Указывает, что это сервисный слой (бизнес-логика)
Spring автоматически создаст экземпляр этого класса
Класс будет управляться Spring контейнером

## конструкторная инъекция для бинов рекомендуема

Следующий пример показывает @Service Bean, который использует конструкторную инъекцию для получения необходимого bean:RiskAssessor
```

import org.springframework.stereotype.Service;

@Service
public class MyAccountService implements AccountService {

	private final RiskAssessor riskAssessor;

	public MyAccountService(RiskAssessor riskAssessor) {
		this.riskAssessor = riskAssessor;
	}

	// ...

}
```
**Если у бобов несколько конструкторов, нужно отметить тот, который вы хотите использовать для Spring, @Autowired:**
```
import java.io.PrintStream;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class MyAccountService implements AccountService {

	private final RiskAssessor riskAssessor;

	private final PrintStream out;

	@Autowired
	public MyAccountService(RiskAssessor riskAssessor) {
		this.riskAssessor = riskAssessor;
		this.out = System.out;
	}

	public MyAccountService(RiskAssessor riskAssessor, PrintStream out) {
		this.riskAssessor = riskAssessor;
		this.out = out;
	}

	// ...

}
```
