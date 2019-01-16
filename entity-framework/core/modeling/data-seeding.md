---
title: Заполнение данными — EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 1c450b142573368d043430f55a3144b6696a8691
ms.sourcegitcommit: b4a5ed177b86bf7f81602106dab6b4acc18dfc18
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54316638"
---
# <a name="data-seeding"></a><span data-ttu-id="af728-102">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="af728-102">Data Seeding</span></span>

<span data-ttu-id="af728-103">Заполнение данными — это процесс заполнения базы данных с исходного набора данных.</span><span class="sxs-lookup"><span data-stu-id="af728-103">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="af728-104">Это можно сделать в EF Core несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="af728-104">There are several ways this can be accomplished in EF Core:</span></span>
* <span data-ttu-id="af728-105">Начальное значение данных модели</span><span class="sxs-lookup"><span data-stu-id="af728-105">Model seed data</span></span>
* <span data-ttu-id="af728-106">Настройка миграции вручную</span><span class="sxs-lookup"><span data-stu-id="af728-106">Manual migration customization</span></span>
* <span data-ttu-id="af728-107">Логику пользовательского инициализации</span><span class="sxs-lookup"><span data-stu-id="af728-107">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="af728-108">Начальное значение данных модели</span><span class="sxs-lookup"><span data-stu-id="af728-108">Model seed data</span></span>

> [!NOTE]
> <span data-ttu-id="af728-109">Это новая возможность в EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="af728-109">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="af728-110">В отличие от в EF6 в EF Core, заполнение данных можно связать с типом сущности как часть конфигурации модели.</span><span class="sxs-lookup"><span data-stu-id="af728-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="af728-111">Затем EF Core [миграций](xref:core/managing-schemas/migrations/index) может автоматически вычислить, что вставить, обновить или удалить операции необходимо применить при обновлении базы данных до новой версии модели.</span><span class="sxs-lookup"><span data-stu-id="af728-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="af728-112">Миграция рассматривает изменения модели только в том случае, при определении, какая операция должна выполняться для получения начального значения данных в нужном состоянии.</span><span class="sxs-lookup"><span data-stu-id="af728-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="af728-113">Таким образом любые изменения данных, выполнены не миграции могут быть потеряны или вызывать ошибку.</span><span class="sxs-lookup"><span data-stu-id="af728-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="af728-114">Например, это настроить начальные данные для `Blog` в `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="af728-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="af728-115">Добавление сущностей, которые имеют отношение значения внешнего ключа необходимо указать:</span><span class="sxs-lookup"><span data-stu-id="af728-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="af728-116">Если тип сущности имеет какие-либо свойства в состоянии тени анонимный класс можно использовать для предоставления значений:</span><span class="sxs-lookup"><span data-stu-id="af728-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="af728-117">Принадлежащих сущностей, типы могут заполняться данными аналогичным образом:</span><span class="sxs-lookup"><span data-stu-id="af728-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="af728-118">См. в разделе [полный пример проекта](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) дополнительный контекст.</span><span class="sxs-lookup"><span data-stu-id="af728-118">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="af728-119">После добавления данных в модель [миграций](xref:core/managing-schemas/migrations/index) следует использовать для применения изменений.</span><span class="sxs-lookup"><span data-stu-id="af728-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="af728-120">Если вам нужно применить миграции как часть автоматизированного развертывания вы можете [создать сценарий SQL](xref:core/managing-schemas/migrations/index#generate-sql-scripts) , можно просмотреть перед выполнением.</span><span class="sxs-lookup"><span data-stu-id="af728-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="af728-121">Кроме того, можно использовать `context.Database.EnsureCreated()` для создания новой базы данных, содержащей начальные данные, например для тестовой базы данных или при использовании поставщика в памяти или любой базы данных не отношения.</span><span class="sxs-lookup"><span data-stu-id="af728-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="af728-122">Обратите внимание, что если база данных уже существует, `EnsureCreated()` не обновит данные схемы, ни начальное значение в базе данных.</span><span class="sxs-lookup"><span data-stu-id="af728-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="af728-123">Для реляционных баз данных не следует вызывать `EnsureCreated()` Если вы планируете использовать миграций.</span><span class="sxs-lookup"><span data-stu-id="af728-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="af728-124">Ограничения модели начальные данные</span><span class="sxs-lookup"><span data-stu-id="af728-124">Limitations of model seed data</span></span>

<span data-ttu-id="af728-125">Начальное значение данных этого типа осуществляется с помощью миграций и скрипт для обновления данных, который уже находится в базе данных необходимо создать без подключения к базе данных.</span><span class="sxs-lookup"><span data-stu-id="af728-125">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="af728-126">Это накладывает некоторые ограничения:</span><span class="sxs-lookup"><span data-stu-id="af728-126">This imposes some restrictions:</span></span>
* <span data-ttu-id="af728-127">Значение первичного ключа необходимо указать, даже если он обычно формируется по базе данных.</span><span class="sxs-lookup"><span data-stu-id="af728-127">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="af728-128">Он будет использоваться для обнаружения изменений данных между миграций.</span><span class="sxs-lookup"><span data-stu-id="af728-128">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="af728-129">Ранее заполненные данные будут удалены, если первичный ключ изменяется каким-либо образом.</span><span class="sxs-lookup"><span data-stu-id="af728-129">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="af728-130">Поэтому эта функция наиболее полезна для статических данных, который имеет предположительно неизменной вне миграции и не зависит от любых других в базе данных, например, ПОЧТОВЫЕ индексы.</span><span class="sxs-lookup"><span data-stu-id="af728-130">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="af728-131">Если ваш сценарий включает следующие рекомендуется использовать логику пользовательской инициализации, описанных в предыдущем разделе:</span><span class="sxs-lookup"><span data-stu-id="af728-131">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>
* <span data-ttu-id="af728-132">Временные данные для тестирования</span><span class="sxs-lookup"><span data-stu-id="af728-132">Temporary data for testing</span></span>
* <span data-ttu-id="af728-133">Данные, зависящие от состояния базы данных</span><span class="sxs-lookup"><span data-stu-id="af728-133">Data that depends on database state</span></span>
* <span data-ttu-id="af728-134">Данные, которые требуются значения ключа, чтобы создать в базе данных, включая сущности, используемый для идентификации альтернативные ключи</span><span class="sxs-lookup"><span data-stu-id="af728-134">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="af728-135">Данных, который требует преобразования (которое не обрабатывается [значение преобразования](xref:core/modeling/value-conversions)), например некоторые хэширование паролей</span><span class="sxs-lookup"><span data-stu-id="af728-135">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="af728-136">Данные, требующие вызовы к внешнему интерфейсу API, например, Создание ролей и пользователей удостоверения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af728-136">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="af728-137">Настройка миграции вручную</span><span class="sxs-lookup"><span data-stu-id="af728-137">Manual migration customization</span></span>

<span data-ttu-id="af728-138">При добавлении миграции изменения в данных, указанных с помощью `HasData` преобразуются в вызовы `InsertData()`, `UpdateData()`, и `DeleteData()`.</span><span class="sxs-lookup"><span data-stu-id="af728-138">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="af728-139">Один из способов обойти некоторые ограничения `HasData` можно вручную добавить эти вызовы или [пользовательских операций](xref:core/managing-schemas/migrations/operations) миграции вместо этого.</span><span class="sxs-lookup"><span data-stu-id="af728-139">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="af728-140">Логику пользовательского инициализации</span><span class="sxs-lookup"><span data-stu-id="af728-140">Custom initialization logic</span></span>

<span data-ttu-id="af728-141">Простой и мощный способ выполнить заполнение данными является использование [ `DbContext.SaveChanges()` ](xref:core/saving/index) до основного приложения логики начала выполнения.</span><span class="sxs-lookup"><span data-stu-id="af728-141">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="af728-142">Код заполнения не должны быть частью выполнения обычное приложение, как это может вызвать проблемы параллелизма, если несколько экземпляров, работают, а также потребует выполнения приложения, у которых есть разрешение на изменение схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="af728-142">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="af728-143">В зависимости от ограничений развертывания код инициализации можно выполнить разными способами:</span><span class="sxs-lookup"><span data-stu-id="af728-143">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>
* <span data-ttu-id="af728-144">Локального запуска приложения инициализации</span><span class="sxs-lookup"><span data-stu-id="af728-144">Running the initialization app locally</span></span>
* <span data-ttu-id="af728-145">Развертывание приложения инициализации с основного приложения, вызов процедуры инициализации и отключении или удалении его инициализации.</span><span class="sxs-lookup"><span data-stu-id="af728-145">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="af728-146">Это обычно может быть автоматизирована с помощью [профилей публикации](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="af728-146">This can usually be automated by using [publish profiles](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
