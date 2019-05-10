---
title: Типы сущностей с помощью конструкторов — EF Core
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: 5bf49718f02c1860871b1f4c255ec4d98fce2fc7
ms.sourcegitcommit: 960e42a01b3a2f76da82e074f64f52252a8afecc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405247"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="164e8-102">Типы сущностей с помощью конструкторов</span><span class="sxs-lookup"><span data-stu-id="164e8-102">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="164e8-103">Это новая возможность в EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="164e8-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="164e8-104">Начиная с EF Core 2.1, стало возможным определять конструктор с параметрами и посредством вызова этого конструктора, при создании экземпляра сущности EF Core.</span><span class="sxs-lookup"><span data-stu-id="164e8-104">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="164e8-105">Параметры конструктора могут быть привязаны к сопоставленным свойствам или на различные виды услуг, которые помогают такое поведение, как отложенной загрузки.</span><span class="sxs-lookup"><span data-stu-id="164e8-105">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="164e8-106">В версии EF Core 2.1 все привязки конструктора является по соглашению.</span><span class="sxs-lookup"><span data-stu-id="164e8-106">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="164e8-107">Конфигурация из определенных конструкторов для использования планируется в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="164e8-107">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="164e8-108">Привязка к сопоставленным свойствам</span><span class="sxs-lookup"><span data-stu-id="164e8-108">Binding to mapped properties</span></span>

<span data-ttu-id="164e8-109">Рассмотрим типичный модель сообщение в блоге:</span><span class="sxs-lookup"><span data-stu-id="164e8-109">Consider a typical Blog/Post model:</span></span>

``` csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="164e8-110">Когда EF Core создает экземпляры этих типов, например для результатов запроса, он сначала вызвать конструктор без параметров по умолчанию и затем в базе данных присвоено значение каждого свойства.</span><span class="sxs-lookup"><span data-stu-id="164e8-110">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="164e8-111">Тем не менее если EF Core находит параметризованный конструктор с имена параметров и типы, которые совпадают с сопоставлены свойства, то он вместо этого будет вызывать параметризованный конструктор со значениями этих свойств и не будет устанавливать каждое свойство явным образом.</span><span class="sxs-lookup"><span data-stu-id="164e8-111">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="164e8-112">Пример:</span><span class="sxs-lookup"><span data-stu-id="164e8-112">For example:</span></span>

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```
<span data-ttu-id="164e8-113">Некоторые другие примечания:</span><span class="sxs-lookup"><span data-stu-id="164e8-113">Some things to note:</span></span>
* <span data-ttu-id="164e8-114">Не все свойства должны иметь параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="164e8-114">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="164e8-115">Например свойство Post.Content не задано каким-либо конструктор параметром, поэтому EF Core установит его после вызова конструктора обычным способом.</span><span class="sxs-lookup"><span data-stu-id="164e8-115">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="164e8-116">Типы параметров и имена должны совпадать имена и типы свойств, за исключением того, что свойства могут быть стиле Pascal хотя параметры являются в стиле Camel.</span><span class="sxs-lookup"><span data-stu-id="164e8-116">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="164e8-117">EF Core не удалось установить свойства навигации (например, блогов или сообщений выше) с помощью конструктора.</span><span class="sxs-lookup"><span data-stu-id="164e8-117">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="164e8-118">Конструктор может быть открытым, закрытым, или иметь любой уровень доступа.</span><span class="sxs-lookup"><span data-stu-id="164e8-118">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="164e8-119">Однако прокси отложенной загрузки требуется, что конструктор доступен из наследуемого класса прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="164e8-119">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="164e8-120">Обычно это означает, что либо открытый или защищенный.</span><span class="sxs-lookup"><span data-stu-id="164e8-120">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="164e8-121">Свойства только для чтения</span><span class="sxs-lookup"><span data-stu-id="164e8-121">Read-only properties</span></span>

<span data-ttu-id="164e8-122">После установки свойств с помощью конструктора имеет смысл сделать некоторые из них только для чтения.</span><span class="sxs-lookup"><span data-stu-id="164e8-122">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="164e8-123">EF Core поддерживает это, но есть несколько моментов, нужно обратить внимание:</span><span class="sxs-lookup"><span data-stu-id="164e8-123">EF Core supports this, but there are some things to look out for:</span></span>
* <span data-ttu-id="164e8-124">Свойства без методов задания не сопоставлены по соглашению.</span><span class="sxs-lookup"><span data-stu-id="164e8-124">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="164e8-125">(Таким образом, как правило, для сопоставления свойства, которые не должны сопоставляться, например вычисляемых свойств.)</span><span class="sxs-lookup"><span data-stu-id="164e8-125">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="164e8-126">С помощью автоматического формирования значений ключей требуется ключевое свойство, чтение и запись, так как значение ключа необходимо задать генератором ключа при вставке новых сущностей.</span><span class="sxs-lookup"><span data-stu-id="164e8-126">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="164e8-127">Для использования закрытых методов задания — простой способ избежать такие вещи.</span><span class="sxs-lookup"><span data-stu-id="164e8-127">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="164e8-128">Пример:</span><span class="sxs-lookup"><span data-stu-id="164e8-128">For example:</span></span>
``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}
```
<span data-ttu-id="164e8-129">EF Core воспринимает свойство с закрытый метод задания как чтения и записи, что означает, что все свойства сопоставляются как и раньше, и ключ можно по-прежнему создаваться хранилища.</span><span class="sxs-lookup"><span data-stu-id="164e8-129">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="164e8-130">Альтернативой использованию закрытых методов задания является сделать свойства действительно только для чтения и Добавление более явное сопоставление в OnModelCreating.</span><span class="sxs-lookup"><span data-stu-id="164e8-130">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="164e8-131">Аналогично некоторые свойства можно полностью удалить и заменить только поля.</span><span class="sxs-lookup"><span data-stu-id="164e8-131">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="164e8-132">Например рассмотрим эти типы сущностей:</span><span class="sxs-lookup"><span data-stu-id="164e8-132">For example, consider these entity types:</span></span>

``` csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```
<span data-ttu-id="164e8-133">И эта конфигурация в OnModelCreating:</span><span class="sxs-lookup"><span data-stu-id="164e8-133">And this configuration in OnModelCreating:</span></span>
``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```
<span data-ttu-id="164e8-134">Сведения:</span><span class="sxs-lookup"><span data-stu-id="164e8-134">Things to note:</span></span>
* <span data-ttu-id="164e8-135">Теперь поле является ключ «свойство».</span><span class="sxs-lookup"><span data-stu-id="164e8-135">The key "property" is now a field.</span></span> <span data-ttu-id="164e8-136">Это не `readonly` таким образом, чтобы сформированные хранилищем ключи могут использоваться.</span><span class="sxs-lookup"><span data-stu-id="164e8-136">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="164e8-137">Другие свойства являются только для чтения свойства, заданные только в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="164e8-137">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="164e8-138">Если значение первичного ключа является только заданные EF или прочитать из базы данных, нет необходимости включать его в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="164e8-138">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="164e8-139">Это оставляет ключ «свойство» как простое поле и позволяет понять, что он не должен задаваться явным образом при создании нового блогов или сообщений.</span><span class="sxs-lookup"><span data-stu-id="164e8-139">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="164e8-140">Этот код приведет к компилятор выдает предупреждение "169", указывающее, что поле никогда не используется.</span><span class="sxs-lookup"><span data-stu-id="164e8-140">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="164e8-141">Это сообщение можно проигнорировать, так как на самом деле EF Core использует поле extralinguistic образом.</span><span class="sxs-lookup"><span data-stu-id="164e8-141">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="164e8-142">Добавление служб</span><span class="sxs-lookup"><span data-stu-id="164e8-142">Injecting services</span></span>

<span data-ttu-id="164e8-143">EF Core также можно внедрить «службы» в конструктор типа сущности.</span><span class="sxs-lookup"><span data-stu-id="164e8-143">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="164e8-144">Например ниже можно ввести:</span><span class="sxs-lookup"><span data-stu-id="164e8-144">For example, the following can be injected:</span></span>
* <span data-ttu-id="164e8-145">`DbContext` -текущий экземпляр контекста, можно также ввести производный тип DbContext</span><span class="sxs-lookup"><span data-stu-id="164e8-145">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="164e8-146">`ILazyLoader` -отложенная загрузка службы — см. в разделе [отложенной загрузки документации](../querying/related-data.md) подробности</span><span class="sxs-lookup"><span data-stu-id="164e8-146">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="164e8-147">`Action<object, string>` -Делегат отложенной загрузки — см. в разделе [отложенной загрузки документации](../querying/related-data.md) подробности</span><span class="sxs-lookup"><span data-stu-id="164e8-147">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="164e8-148">`IEntityType` — EF Core метаданные, связанные с этим типом сущности</span><span class="sxs-lookup"><span data-stu-id="164e8-148">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="164e8-149">Начиная с EF Core 2.1 можно внедрить только те службы, которые известны EF Core.</span><span class="sxs-lookup"><span data-stu-id="164e8-149">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="164e8-150">Поддержка добавления службы приложений будет рассмотрен в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="164e8-150">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="164e8-151">Например можно использовать внедренного DbContext выборочный доступ к базе данных для получения сведений о связанных сущностей, не загружая их все.</span><span class="sxs-lookup"><span data-stu-id="164e8-151">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="164e8-152">В следующем примере это используется для получения числа записей в блог без загрузки в записях:</span><span class="sxs-lookup"><span data-stu-id="164e8-152">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

``` csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```
<span data-ttu-id="164e8-153">Несколько замечания об этом.</span><span class="sxs-lookup"><span data-stu-id="164e8-153">A few things to notice about this:</span></span>
* <span data-ttu-id="164e8-154">Конструктор является закрытым, так как только вызывается с помощью EF Core, а другой открытый конструктор для общего использования.</span><span class="sxs-lookup"><span data-stu-id="164e8-154">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="164e8-155">Код, с помощью внедренного службы (то есть контекст) имеет защищенный характер: на ее основе, `null` для обработки случаев, где EF Core не создает экземпляр.</span><span class="sxs-lookup"><span data-stu-id="164e8-155">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="164e8-156">Так как службы хранится в свойстве чтения и записи будет изменено при присоединении сущности в новый экземпляр контекста.</span><span class="sxs-lookup"><span data-stu-id="164e8-156">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="164e8-157">Внедрение DbContext, как это часто считается антишаблоном так, как он связывает типах сущностей непосредственно в EF Core.</span><span class="sxs-lookup"><span data-stu-id="164e8-157">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="164e8-158">Внимательно рассмотрите все параметры, прежде чем с помощью внесения службы следующим образом.</span><span class="sxs-lookup"><span data-stu-id="164e8-158">Carefully consider all options before using service injection like this.</span></span>
