### DevTools — это набор инструментов, которые ускоряют и упрощают разработку Spring Boot приложений. Это как "горячие клавиши" для Spring Boot!
например
```
# БЕЗ DevTools:
1. Изменили код
2. Остановили приложение (Ctrl+C)
3. Запустили заново (./mvnw spring-boot:run)
4. Ждем 15-30 секунд
# 💀 Убивает время и концентрацию!

# С DevTools:
1. Изменили код
2. Сохранили файл (Ctrl+S)
3. Приложение перезапустилось за 2-3 секунды!
# 🚀 Сохраняет время и фокус!
```

```
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<optional>true</optional>
	</dependency>
</dependencies>
```
_Devtools могут вызывать проблемы с загрузкой классов, особенно в многомодульных проектах. Диагностика проблем с загрузкой классов объясняет, как их диагностировать и решать
По умолчанию DevTools НЕ включаются в production JAR (fat jar)._


**Maven: optional=true**
```
<dependency>


    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>  <!-- Ключевая строка! -->
</dependency>
```
Что делает optional=true:
DevTools доступны в вашем проекте
Не передаются зависимым проектам
Зависимые проекты не увидят DevTools


### SpringApplication
Класс SpringApplication предоставляет удобный способ загрузки приложения Spring, который запускается с определённого метода. Во многих случаях можно делегировать на статический метод SpringApplication.run(Class, String...), как показано в следующем примере:main()
```
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MyApplication {

	public static void main(String[] args) {
		SpringApplication.run(MyApplication.class, args);
	}

}
```

### Ленивая инициализация в Spring Boot
**🎯 Что такое ленивая инициализация?**
Обычная инициализация: Все бины создаются при запуске приложения.
Ленивая инициализация: Бины создаются только при первом использовании.

⚡ Преимущества ленивой инициализации:
✅ Быстрый старт приложения
✅ Меньше памяти при запуске
✅ Отложенная загрузка тяжелых бинов

⚠️ Недостатки:
❌ Первое обращение к бину медленнее
❌ Позднее обнаружение ошибок конфигурации
❌ Не подходит для всех сценариев

🔧 Как включить:
1. Через application.properties:
```
spring.main.lazy-initialization=true
```

2. Через application.yml:
```
spring:
  main:
    lazy-initialization: true
```

3. Программно через SpringApplication:
```
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(Application.class);
        app.setLazyInitialization(true);  // ← Включаем
        app.run(args);
    }
}
```

4. Через SpringApplicationBuilder:
```
new SpringApplicationBuilder(Application.class)
    .lazyInitialization(true)  // ← Включаем
    .run(args);
```

**🎮 Смешанный подход:**
Общая ленивая + исключения:
```
// 1. Включаем ленивую инициализацию для всего приложения
spring.main.lazy-initialization=true

// 2. Для конкретных бинов отключаем ленивость
@Configuration
public class Config {
    
    @Bean
    @Lazy(false)  // ← Этот бин загрузится сразу
    public DataSource dataSource() {
        return new HikariDataSource();
    }
    
    @Bean  // ← Этот бин будет ленивым (по умолчанию)
    public UserService userService() {
        return new UserService();
    }
}
```

#### Баннеры
```
${AnsiColor.BRIGHT_CYAN}
  _____ _____ ____  _____  ______ _____  
 |  __ \_   _/ __ \|  __ \|  ____|  __ \ 
 | |__) || || |  | | |__) | |__  | |__) |
 |  ___/ | || |  | |  _  /|  __| |  _  / 
 | |    _| || |__| | | \ \| |____| | \ \ 
 |_|   |_____\____/|_|  \_\______|_|  \_\
${AnsiColor.GREEN}
Version: ${application.formatted-version}
${AnsiColor.DEFAULT}
```

### Настройка SpringApplication
Если настройки SpringApplication по умолчанию вам не по душе, вы можете создать локальный экземпляр и настроить его. Например, чтобы выключить баннер, можно написать:
```
import org.springframework.boot.Banner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MyApplication {

	public static void main(String[] args) {
		SpringApplication application = new SpringApplication(MyApplication.class);
		application.setBannerMode(Banner.Mode.OFF);
		application.run(args);
	}

}
```

