---
title: Автоматическое обнаружение изменений — EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
ms.openlocfilehash: 9af85fd7ca48a14432a1f33c59079fc438ef8810
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414383"
---
# <a name="automatic-detect-changes"></a><span data-ttu-id="8154c-102">Автоматическое обнаружение изменений</span><span class="sxs-lookup"><span data-stu-id="8154c-102">Automatic detect changes</span></span>
<span data-ttu-id="8154c-103">При использовании большинства сущностей POCO определение того, каким образом изменилась сущность (и, следовательно, какие обновления необходимо отправить в базу данных), обрабатывается алгоритмом обнаружения изменений.</span><span class="sxs-lookup"><span data-stu-id="8154c-103">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="8154c-104">Обнаружение изменений осуществляется путем обнаружения различий между текущими значениями свойств сущности и значениями исходных свойств, которые хранятся в моментальном снимке при запросе или присоединении сущности.</span><span class="sxs-lookup"><span data-stu-id="8154c-104">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="8154c-105">Методы, представленные в этом разделе, также применимы к моделям, созданным с помощью Code First и конструктора EF.</span><span class="sxs-lookup"><span data-stu-id="8154c-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="8154c-106">По умолчанию Entity Framework выполняет обнаружение изменений автоматически при вызове следующих методов:</span><span class="sxs-lookup"><span data-stu-id="8154c-106">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="8154c-107">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="8154c-107">DbSet.Find</span></span>  
- <span data-ttu-id="8154c-108">DbSet. local</span><span class="sxs-lookup"><span data-stu-id="8154c-108">DbSet.Local</span></span>  
- <span data-ttu-id="8154c-109">DbSet. Add</span><span class="sxs-lookup"><span data-stu-id="8154c-109">DbSet.Add</span></span>  
- <span data-ttu-id="8154c-110">DbSet. AddRange</span><span class="sxs-lookup"><span data-stu-id="8154c-110">DbSet.AddRange</span></span>
- <span data-ttu-id="8154c-111">DbSet. Remove</span><span class="sxs-lookup"><span data-stu-id="8154c-111">DbSet.Remove</span></span>  
- <span data-ttu-id="8154c-112">DbSet. Ремоверанже</span><span class="sxs-lookup"><span data-stu-id="8154c-112">DbSet.RemoveRange</span></span>
- <span data-ttu-id="8154c-113">DbSet. Attach</span><span class="sxs-lookup"><span data-stu-id="8154c-113">DbSet.Attach</span></span>  
- <span data-ttu-id="8154c-114">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="8154c-114">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="8154c-115">DbContext. Жетвалидатионеррорс</span><span class="sxs-lookup"><span data-stu-id="8154c-115">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="8154c-116">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="8154c-116">DbContext.Entry</span></span>  
- <span data-ttu-id="8154c-117">Дбчанжетраккер. записи</span><span class="sxs-lookup"><span data-stu-id="8154c-117">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="8154c-118">Отключение автоматического обнаружения изменений</span><span class="sxs-lookup"><span data-stu-id="8154c-118">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="8154c-119">При отслеживании большого количества сущностей в контексте и многократном вызове одного из этих методов в цикле вы можете значительно повысить производительность, отключив обнаружение изменений в течение цикла.</span><span class="sxs-lookup"><span data-stu-id="8154c-119">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="8154c-120">Пример:</span><span class="sxs-lookup"><span data-stu-id="8154c-120">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    try
    {
        context.Configuration.AutoDetectChangesEnabled = false;

        // Make many calls in a loop
        foreach (var blog in aLotOfBlogs)
        {
            context.Blogs.Add(blog);
        }
    }
    finally
    {
        context.Configuration.AutoDetectChangesEnabled = true;
    }
}
```  

<span data-ttu-id="8154c-121">Не забудьте повторно включить обнаружение изменений после цикла — мы использовали конструкцию try/finally, чтобы убедиться, что она всегда включена повторно, даже если код в цикле вызывает исключение.</span><span class="sxs-lookup"><span data-stu-id="8154c-121">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="8154c-122">Альтернативой отключению и повторному включению является автоматическое обнаружение отключенных изменений в любое время и либо контекст вызова. ChangeTracker. DetectChanges явным образом или используют прокси-серверы отслеживания изменений более тщательно.</span><span class="sxs-lookup"><span data-stu-id="8154c-122">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="8154c-123">Оба эти варианта являются расширенными и могут легко добавить в приложение небольшие ошибки, чтобы использовать их с осторожностью.</span><span class="sxs-lookup"><span data-stu-id="8154c-123">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="8154c-124">Если необходимо добавить или удалить несколько объектов из контекста, рассмотрите возможность использования DbSet. AddRange и DbSet. Ремоверанже.</span><span class="sxs-lookup"><span data-stu-id="8154c-124">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="8154c-125">Эти методы автоматически обнаруживают изменения только один раз после завершения операций добавления или удаления.</span><span class="sxs-lookup"><span data-stu-id="8154c-125">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
