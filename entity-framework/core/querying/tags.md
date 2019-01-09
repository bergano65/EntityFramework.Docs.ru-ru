---
title: Теги запросов — EF Core
author: divega
ms.date: 11/14/2018
ms.assetid: 73C7A627-C8E9-452D-9CD5-AFCC8FEFE395
uid: core/querying/tags
ms.openlocfilehash: 3a4d516cab5836c659e42d825c4f1bf89355d671
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688769"
---
# <a name="query-tags"></a><span data-ttu-id="2732b-102">Теги запросов</span><span class="sxs-lookup"><span data-stu-id="2732b-102">Query tags</span></span>
> [!NOTE]
> <span data-ttu-id="2732b-103">Это новая функция в EF Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="2732b-103">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="2732b-104">Эта функция помогает сопоставить запросы LINQ в коде со сгенерированными запросами SQL, зарегистрированными в журналах.</span><span class="sxs-lookup"><span data-stu-id="2732b-104">This feature helps correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="2732b-105">Мы можете пометить запрос LINQ с помощью нового метода `TagWith()`:</span><span class="sxs-lookup"><span data-stu-id="2732b-105">You annotate a LINQ query using the new `TagWith()` method:</span></span> 

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="2732b-106">Этот запрос LINQ преобразуется в следующую инструкцию SQL:</span><span class="sxs-lookup"><span data-stu-id="2732b-106">This LINQ query is translated to the following SQL statement:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="2732b-107">Метод `TagWith()` можно вызвать много раз для одного и того же запроса.</span><span class="sxs-lookup"><span data-stu-id="2732b-107">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="2732b-108">Теги запросов являются кумулятивными.</span><span class="sxs-lookup"><span data-stu-id="2732b-108">Query tags are cumulative.</span></span>
<span data-ttu-id="2732b-109">Например, при использовании следующих методов</span><span class="sxs-lookup"><span data-stu-id="2732b-109">For example, given the following methods:</span></span>

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

<span data-ttu-id="2732b-110">этот запрос</span><span class="sxs-lookup"><span data-stu-id="2732b-110">The following query:</span></span>   

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

<span data-ttu-id="2732b-111">преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="2732b-111">Translates to:</span></span>

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="2732b-112">В качестве тегов запросов можно использовать многострочные литералы.</span><span class="sxs-lookup"><span data-stu-id="2732b-112">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="2732b-113">Пример:</span><span class="sxs-lookup"><span data-stu-id="2732b-113">For example:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

<span data-ttu-id="2732b-114">Преобразуется в следующий запрос SQL:</span><span class="sxs-lookup"><span data-stu-id="2732b-114">Produces the following SQL:</span></span>

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="2732b-115">Известные ограничения</span><span class="sxs-lookup"><span data-stu-id="2732b-115">Known limitations</span></span>
<span data-ttu-id="2732b-116">**Теги запросов не поддерживают параметризацию** — EF Core всегда обрабатывает теги в запросе LINQ как строковые литералы, которые включаются в сгенерированный запрос SQL.</span><span class="sxs-lookup"><span data-stu-id="2732b-116">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="2732b-117">Скомпилированные запросы, принимающие теги запросов в качестве параметров, не допускаются.</span><span class="sxs-lookup"><span data-stu-id="2732b-117">Compiled queries that take query tags as parameters aren't allowed.</span></span>