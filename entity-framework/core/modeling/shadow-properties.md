---
title: Свойства тени — EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 229cfd83f75b01dff9ac9ad30ee55c7cc727c19e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414713"
---
# <a name="shadow-properties"></a>Свойства тени

Теневые свойства — это свойства, которые не определены в классе сущности .NET, но определены для этого типа сущности в модели EF Core. Значения и состояния этих свойств хранятся исключительно в средстве отслеживания изменений. Теневые свойства полезны при наличии в базе данных данных, которые не должны быть представлены в сопоставленных типах сущностей.

## <a name="foreign-key-shadow-properties"></a>Свойства тени внешнего ключа

Свойства теневой копии чаще всего используются для свойств внешнего ключа, где связь между двумя сущностями представляется значением внешнего ключа в базе данных, но Управление связью осуществляется на основе типов сущностей с помощью свойств навигации между сущностью. типов. По соглашению EF будет представлять теневое свойство при обнаружении связи, но свойство внешнего ключа не найдено в зависимом классе сущности.

Свойство будет называться `<navigation property name><principal key property name>` (Навигация по зависимой сущности, которая указывает на основную сущность, используется для именования). Если имя свойства ключа участника содержит имя свойства навигации, то имя будет просто `<principal key property name>`. Если в зависимой сущности нет свойства навигации, в его месте используется имя типа участника.

Например, приведенный ниже листинг кода приведет к тому, что в сущность `Post` будет введено свойство `BlogId` Shadow:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>Настройка теневых свойств

Для настройки теневых свойств можно использовать API-интерфейс Fluent. После вызова перегрузки строки `Property`можно связать любой из вызовов конфигурации для других свойств. В следующем примере, поскольку `Blog` не имеет свойства CLR с именем `LastUpdated`, создается свойство Shadow:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

Если имя, передаваемое в метод `Property`, совпадает с именем существующего свойства (теневого или определенного для класса сущностей), то код настроит это существующее свойство, а не создаст новое свойство Shadow.

## <a name="accessing-shadow-properties"></a>Доступ к теневым свойствам

Значения теневых свойств можно получить и изменить с помощью `ChangeTracker` API:

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

На теневые свойства в запросах LINQ можно ссылаться с помощью статического метода `EF.Property`:

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```
