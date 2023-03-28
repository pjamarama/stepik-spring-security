# Spring Security
Аутентификация - процедура проверки подлинности юзера (например, путем сравнения логина/пароля).
Авторизация - процедура проверки разрешений юзера на доступ к тому или иному ресурсу.

## Конфигурация приложения
- Добавление необходимых зависимостей
- Создание конфигурационного java-класса
- Создание класса для реализации Dispatcher Servlet
- Добавление в проект Tomcat
- Создание Security Initializer
- Создание конфигурации для Spring Security

## Процедуры аутентификации и авторизации
Создаем простые вьюшки для тестовых целей.

Конфигурационный класс имеет аннотацию @EnableWebSecurity.

Переопределяем методы. Первый содержит в себе креды и отвечает за аутентификацию, второй отвечает за авторизацию:
```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    User.UserBuilder userBuilder = User.withDefaultPasswordEncoder();
    auth.inMemoryAuthentication()
            .withUser(userBuilder.username("zaur").password("zaur").roles("EMPLOYEE"))
            .withUser(userBuilder.username("elena").password("elena").roles("HR"))
            .withUser(userBuilder.username("ivan").password("ivan").roles("MANAGER"));
}

@Override
protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
        .antMatchers("/").hasAnyRole("EMPLOYEE", "HR", "MANAGER")
        .antMatchers("/hr_info").hasRole("HR")
        .antMatchers("/manager_info/**").hasRole("MANAGER")
        .and().formLogin().permitAll();
        }
```

Более правильным будет совсем не отображать элементы вью, если пользователь не авторизован для них. Чтобы сделать это, добавим в зависимости Spring Security Taglibs и пропишем определенные тэги во вью:
```xml
<security:authorize access="hasRole('HR')">
<input type="button" value="salary" onclick="window.location.href = 'hr_info'">
<br><br>
Only for HR staff
</security:authorize>
```