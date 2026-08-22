# Практическое занятие 8: Аутентификация и роли — Spring Security (Часть 2)

## Введение

Вы продолжаете проект, начатый на занятии 7. Сегодня добавляете в него регистрацию, вход в систему и два уровня доступа: **USER** может только просматривать таблицу и входить/выходить из системы; **ADMIN** может добавлять, редактировать и удалять записи. Используется Spring Security.

Откройте проект, созданный на занятии 7 — вся дальнейшая работа ведётся в нём же.

## Часть 1. Подключение Spring Security

### Задание 1.1. Зависимость

Добавьте в `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Перезапустите приложение и откройте `http://localhost:8080/items` — Spring Security автоматически включит форму входа со сгенерированным паролем в консоли. Это ожидаемо: далее вы настроите собственную конфигурацию.

### Задание 1.2. Сущность User и роли

Создайте пакет `mpt.it.app.security` и в нём:

```java
package mpt.it.app.security;

public enum Role {
    USER, ADMIN
}
```

```java
package mpt.it.app.security;

import jakarta.persistence.*;

@Entity
@Table(name = "app_users")
public class AppUser {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(unique = true, nullable = false)
    private String email;

    @Column(nullable = false)
    private String password;

    @Enumerated(EnumType.STRING)
    private Role role;

    public AppUser() {}

    public AppUser(String email, String password, Role role) {
        this.email = email;
        this.password = password;
        this.role = role;
    }

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public String getPassword() { return password; }
    public void setPassword(String password) { this.password = password; }
    public Role getRole() { return role; }
    public void setRole(Role role) { this.role = role; }
}
```

```java
package mpt.it.app.security;

import org.springframework.data.jpa.repository.JpaRepository;
import java.util.Optional;

public interface AppUserRepository extends JpaRepository<AppUser, Long> {
    Optional<AppUser> findByEmail(String email);
}
```

## Часть 2. Аутентификация

### Задание 2.1. UserDetailsService

```java
package mpt.it.app.security;

import org.springframework.security.core.userdetails.*;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.stereotype.Service;

@Service
public class AppUserDetailsService implements UserDetailsService {

    private final AppUserRepository repository;

    public AppUserDetailsService(AppUserRepository repository) {
        this.repository = repository;
    }

    @Override
    public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
        AppUser user = repository.findByEmail(email)
            .orElseThrow(() -> new UsernameNotFoundException("Пользователь не найден"));

        return org.springframework.security.core.userdetails.User
            .withUsername(user.getEmail())
            .password(user.getPassword())
            .authorities(new SimpleGrantedAuthority("ROLE_" + user.getRole().name()))
            .build();
    }
}
```

### Задание 2.2. Конфигурация безопасности

```java
package mpt.it.app.security;

import org.springframework.context.annotation.*;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configurers.AbstractHttpConfigurer;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/login", "/register").permitAll()
                .requestMatchers("/items/save", "/items/delete/**", "/items/edit/**").hasRole("ADMIN")
                .requestMatchers("/items").authenticated()
                .anyRequest().permitAll()
            )
            .formLogin(form -> form
                .loginPage("/login")
                .defaultSuccessUrl("/items", true)
                .permitAll()
            )
            .logout(logout -> logout
                .logoutUrl("/logout")
                .logoutSuccessUrl("/login?logout")
                .permitAll()
            )
            .csrf(AbstractHttpConfigurer::disable);

        return http.build();
    }
}
```

Обратите внимание: маршруты изменения данных (`/items/save`, `/items/delete/**`, `/items/edit/**`) доступны только роли `ADMIN`; сам просмотр списка (`/items`) — любому аутентифицированному пользователю.

## Часть 3. Регистрация и страницы входа

### Задание 3.1. Контроллер регистрации

```java
package mpt.it.app.security;

import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

@Controller
public class AuthController {

    private final AppUserRepository userRepository;
    private final PasswordEncoder passwordEncoder;

    public AuthController(AppUserRepository userRepository, PasswordEncoder passwordEncoder) {
        this.userRepository = userRepository;
        this.passwordEncoder = passwordEncoder;
    }

    @GetMapping("/login")
    public String loginPage() { return "login"; }

    @GetMapping("/register")
    public String registerForm(Model model) {
        model.addAttribute("user", new AppUser());
        return "register";
    }

    @PostMapping("/register")
    public String register(@ModelAttribute AppUser user) {
        user.setPassword(passwordEncoder.encode(user.getPassword()));
        user.setRole(Role.USER); // по умолчанию новый пользователь — USER
        userRepository.save(user);
        return "redirect:/login?registered";
    }
}
```

### Задание 3.2. Шаблоны login.html и register.html

Создайте `src/main/resources/templates/login.html`:

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head><meta charset="UTF-8"><title>Вход</title>
<link rel="stylesheet" th:href="@{/css/style.css}"></head>
<body>
<h2>Вход в систему</h2>
<form th:action="@{/login}" method="post">
    <input type="email" name="username" placeholder="Email" required/>
    <input type="password" name="password" placeholder="Пароль" required/>
    <button type="submit">Войти</button>
</form>
<p><a th:href="@{/register}">Регистрация</a></p>
</body>
</html>
```

Создайте `src/main/resources/templates/register.html` с формой регистрации (email, пароль), похожей по стилю на `login.html`, использующей `th:object="${user}"` и `th:field="*{email}"` / `th:field="*{password}"`.

### Задание 3.3. Ссылка выхода в items.html

Добавьте в `items.html` (в шапку страницы):

```html
<form th:action="@{/logout}" method="post">
    <button type="submit">Выйти</button>
</form>
```

## Часть 4. Скрытие элементов управления по роли

### Задание 4.1. Подключение Thymeleaf Security Dialect

Добавьте зависимость:

```xml
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-springsecurity6</artifactId>
</dependency>
```

В `items.html` добавьте пространство имён:

```html
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
```

### Задание 4.2. Скрытие формы и кнопок для USER

Оберните форму добавления/редактирования и кнопки «Редактировать»/«Удалить» атрибутом:

```html
<div sec:authorize="hasRole('ADMIN')">
    <!-- форма добавления и кнопки редактирования/удаления -->
</div>
```

Теперь пользователь с ролью `USER` видит только таблицу и не видит элементов управления, даже если попытается обратиться к защищённому маршруту напрямую (это дополнительно блокируется на уровне `SecurityConfig`).

## Часть 5. Начальные данные и проверка

### Задание 5.1. Создание тестовых пользователей при старте

```java
package mpt.it.app.security;

import jakarta.annotation.PostConstruct;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Component;

@Component
public class SecurityInitializer {

    private final AppUserRepository userRepository;
    private final PasswordEncoder passwordEncoder;

    public SecurityInitializer(AppUserRepository userRepository, PasswordEncoder passwordEncoder) {
        this.userRepository = userRepository;
        this.passwordEncoder = passwordEncoder;
    }

    @PostConstruct
    public void init() {
        if (userRepository.count() == 0) {
            userRepository.save(new AppUser("user@test.com", passwordEncoder.encode("user123"), Role.USER));
            userRepository.save(new AppUser("admin@test.com", passwordEncoder.encode("admin123"), Role.ADMIN));
            System.out.println("Созданы тестовые пользователи: user@test.com / admin@test.com");
        }
    }
}
```

### Задание 5.2. Проверка ролей

Запустите приложение и проверьте:

1. Войдите как `user@test.com` / `user123`. Убедитесь, что вы видите таблицу, но не видите форму добавления и кнопки редактирования/удаления.
2. Войдите как `admin@test.com` / `admin123`. Убедитесь, что можете добавлять, редактировать и удалять записи.
3. Попробуйте открыть `/items/delete/1` под учёткой `USER` напрямую через адресную строку — что происходит?
4. Зарегистрируйте нового пользователя через `/register` — с какой ролью он создаётся по умолчанию?
5. Выйдите из системы через кнопку «Выйти» — что происходит с доступом к `/items`?

## Контрольные вопросы

1. Чем отличается `hasRole("ADMIN")` от `hasAuthority("ROLE_ADMIN")`?
2. Зачем нужен `PasswordEncoder`? Что случится, если сохранить пароль в базе без шифрования?
3. Что делает `UserDetailsService` и когда Spring Security его вызывает?
4. В чём разница между ограничением доступа в `SecurityConfig` (через `requestMatchers`) и скрытием элемента в шаблоне через `sec:authorize`? Почему нужны оба?
5. Что произойдёт при попытке `ADMIN`-маршрута пользователем с ролью `USER`, если он введёт URL вручную?
6. Почему в `SecurityInitializer` пароли шифруются перед сохранением, а не хранятся как есть?

## Что сдать по итогам занятия

- Проект с занятия 7, дополненный: регистрацией, страницей входа, ролями USER/ADMIN;
- USER может только просматривать и входить/выходить, ADMIN может редактировать таблицу;
- элементы управления скрыты для USER в интерфейсе и заблокированы на уровне маршрутов;
- ответы на контрольные вопросы.

**Итог:** к концу занятия 8 у вас есть полноценное индивидуальное веб-приложение — CRUD на вашу тему + аутентификация и разграничение доступа.
