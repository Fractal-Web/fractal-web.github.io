<h1>Подготовка проекта на ASP.NET MVC</h1>
<h3>От инициализации до момента написания брейкпоинтов</h3>
В данной документации я не буду углубляться в детали работы каждого отдельного элемента. Эта штука должна будет служить простой напоминалкой для меня и моих коллег, если кто-то боится пропустить какой-то этап. Более того, документация будет обновляться по мере моего изучения каких-то деталей.

<h2>Создание проекта</h2>
```bash
dotnet new mvc -o directory-name
```
Вводим эту команду в powershell для создания папки *directory-name* и всеми внутренностями нашего бэка.

Далее открываем папку с проектом:
```bash
cd directory-name
```
И начинаем настраивать наше окружение.

Уже на этом этапе можно запустить наш проект и посмотреть на пример, который предлагает нам dotnet. Фронт там сделан с помощью Razor. Если мы делаем на React, то сразу вычищаем всё, что с ним связано.

<h2>Избавляемся от Razor</h2>
1. Удаляем папку `Views`.
2. Удаляем всё, что внутри папки `wwwroot`. Саму папку не трогаем, если планируем загружать какие-то статические файлы для будущего api.
3. Открываем `Program.cs` и удаляем вот эти строчки, если они там есть:

`Program.cs`
```cs
builder.Services.AddControllersWithViews();
```
```cs
if (!app.Environment.IsDevelopment())
{
	app.UseExceptionHandler("/Home/Error");
	app.UseHsts();
}
```
```cs
app.UseStaticFiles();
```

Если редирект на https уже настроен на сервере вручную.

`Program.cs`
```cs
app.UseHttpsRedirection();
```

Если в вашем приложении не будет авторизации, этот миддлвар можно так-же удалять.

`Program.cs`
```cs
app.UseAuthorization();
```

Руты можно настраивать иначе. Тут больше для работы с Razor, от которого мы сейчас и избавляемся.

`Program.cs`
```cs
app.MapControllerRoute(
	name: "default",
	pattern: "{controller=Home}/{action=Index}/{id?}");
```

<h2>Создаём модели</h2>
Удаляем или редактируем модели в папке `Models`. Рекомендую подключить:

`UserModel.cs`
```cs
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
```

С помощью этих системных библиотек мы сможем для каждой сущности модели указывать аннотации, чтобы этого не пришлось делать вручную в базе данных:

`UserModel.cs`
```cs
[Key]
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public Guid Id { get; set; }

[Required]
public string Login { get; set; }

[Phone, MinLength(11)]
public int PhoneNumber { get; set; }
```

Модель создаётся стандартным C# классом, предварительно помещённым в отдельный namespace для моделей

`UserModel.cs`
```cs
namespace TestBackend.Models;

public class User
{
	// here other public elems
}
```

<h2>Создаём контекст базы данных.</h2> 
Создаём в корневой папке (обычно не выделял под контексты отдельную папку, но если есть желание - можете выделить) файл `DatabaseContext.cs`.
Для контекста нам понадобится обязательно библиотека `Micrisoft.EntityFrameworkCore` из которой мы возьмём экземпляр `DbContext`.
Последнюю актуальную версию `Microsoft.EntityFrameworkCore` вы можете посмотреть на www.nuget.org .

В моей практике, контекст всегда помещался в namespace моделей, т.к. он используется зачастую неотрывно от них, поэтому в данном примере я сделаю точно так-же. 

`DatabaseContext.cs`
```cs
using Microsoft.EntityFrameworkCore;

namespace AdminPanelBackend.Models;

public class AdminPanelContext : DbContext
{
   // here some code
}
```

Теперь добавляем внутрь классы под каждую нашу модель, чтобы контекст мог взаимодействовать с базой данных на основе заготовленных моделей. Синтаксис выглядит так:

`DatabaseContext.cs`
```cs
public DbSet<User> Users { get; set; }
```

где User - это IEntity интерфейс, а Users название таблицы в базе данных.

Теперь добавляем параметр options, который позволит нам вытягивать из `Program.cs` конфигурацию для контекста (обычно это один метод GetConnectionString, который берёт из appsettings строку данных для подключения к базе данных). Выглядит это вот так:

`DatabaseContext.cs`
```cs
public AdminPanelContext(DbContextOptions<AdminPanelContext> options) : base(options) 
{ }
```

И сразу подключаем контекст в конструктор

`Program.cs`
```cs
builder.Services.AddDbContext<DatabaseContext>(
	options =>
		options.UseSqlServer(builder.Configuration.GetConnectionString("Database"))
);
```

Теперь, собственно, нужно создать в `appsettings.Development.json` для локальной версии и в `appsettings.Production.json` для продакшн версии эту самую connection string:

`appsettings.Production.json`
```json
"ConnectionStrings" : {
    "Database": "Server={ip-сервера}; Database={имя-базы-данных};User Id={имя-главного-пользователя-базы-данных};Password={пароль-пользователя};TrustServerCertificate=True;"
  }
```

<h2>Обновляем данные в базе данных</h2>
Когда все модели будут готовы, мы можем обновить базу данных, чтобы она создала все нужные таблицы. Для этого нам сначала потребуется установить пакет `Microsoft.EntityFrameworkCore.Design` и после чего создать миграционный файл командой в консоли

```bash
dotnet ef migrations add {имя-апдейта}
```

и произвести саму миграцию

```bash
dotnet ef database update
```

<h2>Делаем контроллеры</h2>
В папке `Controllers` либо удаляем все файлы, либо редактируем под свои нужды. Соответственно, namespace будет Controllers, экземпляр класса будет `Controller`. В верхней части у нас инициализируются все нужные нам классы (в данном случае, логгер для дебага и сам контекст базы данных, для взаимодейтсвия с БД).

`UserController.cs`
```cs
public class userController : Controller
{
    private readonly ILogger<userController> _logger;
    private readonly AdminPanelContext _db;

    public userController(ILogger<userController> logger, AdminPanelContext db)
    {
        _logger = logger;
        _db = db;
    }

	//here some methods
}
```

Добавляем немного аттрибутов над контроллером:

`UserController.cs`
```cs
[ApiController]
[Route("[controller]")]
public class userController : Controller
 // code
{
```

И теперь мы можем спокойно создавать снизу методы по нужным эндпоинтам.
Пример одного из таких:

`UserController.cs`
```cs
[HttpPost("create")]
public async Task<IActionResult> CreateUser(User user)
{
	_db.Users.Add(user);
	await _db.SaveChangesAsync();

	return Ok();
}
```

Для проверки, запускаем наш бекенд 

```bash
dotnet watch run
```

Смотрим в консоли, по какому адресу он запустился. У меня это `localhost:5172`. Значит из запросы будем отправлять туда. Открываем Postman и делаем запрос по нужному эндпоинту:

```
https://{адрес-сервера}/{имя-контроллера}/{имя-роута}
```

В нашем случае

```
https://localhost:5172/user/create
```

И прикрепляем к нему в body блок Json кода по нашей модели:

```json
{
  "User": "Artem",
  "PhoneNumber": "89000000000"
}
```

Отправляем и получаем response 200 (Ok())!