
## PHP код

```php

// Запрет доступа для всех пользователей, не имеющих роль "ROLE_ADMIN"
$this->denyAccessUnlessGranted('ROLE_ADMIN');
// или:
$this->denyAccessUnlessGranted('ROLE_ADMIN', null, 'User tried to access a page without having ROLE_ADMIN');
// или:
if ($this->isGranted('ROLE_ADMIN')) {
    // ...
}

// Тоже самое, только через аннотации
/**
 * Require ROLE_ADMIN for only this controller method.
 *
 * @IsGranted("ROLE_ADMIN")
 */
public function adminDashboard(): Response
{
    // ...
}

// Только для авторизованных
$this->denyAccessUnlessGranted('IS_AUTHENTICATED_FULLY');

// Получить данные текущего пользователя
/** @var \App\Entity\User $user */
$user = $this->getUser();

// Получить пользователя в сервисе
class ExampleService
{
    private $security;

    public function __construct(Security $security)
    {
        $this->security = $security;
    }

    public function someMethod()
    {
        $user = $this->security->getUser();
        // ...
    }
}

// Кинуть исключение, если нет доступа
throw $this->createAccessDeniedException('Access denied.');

// Страница не найдена
throw $this->createNotFoundException('Page not found.');

```

## Шаблоны Twig
```twig
{# Показываем только для пользователя с ролью "ROLE_ADMIN" #}
{% if is_granted('ROLE_ADMIN') %}
    <a href="...">Delete</a>
{% endif %}

{# Показываем только авторизованному #}
{% if is_granted('IS_AUTHENTICATED_FULLY') %}
    <p>Email: {{ app.user.email }}</p>
{% endif %}

```

