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
```cs
app.UseHttpsRedirection();
```
Если в вашем приложении не будет авторизации, этот миддлвар можно так-же удалять.
```cs
app.UseAuthorization();
```
Руты можно настраивать иначе. Тут больше для работы с Razor, от которого мы сейчас и избавляемся.
```cs
app.MapControllerRoute(
	name: "default",
	pattern: "{controller=Home}/{action=Index}/{id?}");
```

<h2>Создаём модели</h2>
Удаляем или редактируем модели в папке `Models`. Рекомендую подключить:
```cs
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
```
С помощью этих системных библиотек мы сможем для каждого состояния модели указывать аннотации, чтобы этого не пришлось делать вручную в базе данных:
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
```cs
namespace TestBackend.Models;

public class User
{
	// here other public elems
}
```

<h2>Создаём контекст базы данных.</h2> 
Допишу позже, в свободное время // temamint