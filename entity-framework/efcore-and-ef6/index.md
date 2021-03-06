---
title: Сравнение EF6 и EF Core
description: Руководство по выбору между EF6 и EF Core.
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: e28c7d0299e5089f56fb0795d00917cfc30f5cf1
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413399"
---
# <a name="compare-ef-core--ef6"></a>Сравнение EF Core и EF6

## <a name="ef-core"></a>EF Core

Entity Framework Core ([EF Core](../core/index.md)) — это современный модуль сопоставления "объект — база данных" для .NET. Он поддерживает запросы LINQ, отслеживание изменений, обновления и миграции схемы.

EF Core работает с SQL Server/SQL Azure, SQLite, Azure Cosmos DB, MySQL, PostgreSQL и многими другими базами данных через [модель подключаемого модуля поставщика базы данных](../core/providers/index.md).

## <a name="ef6"></a>EF6

Entity Framework 6 ([EF6](../ef6/index.md)) — это объектно-реляционный модуль сопоставления, предназначенный для .NET Framework, но с поддержкой для .NET Core. EF6 — это стабильный, поддерживаемый продукт, который больше не разрабатывается.

## <a name="feature-comparison"></a>Сравнение функций

EF Core предлагает новые функции, которые не будут реализованы в EF6. Однако в настоящее время в EF Core реализованы не все функции EF6.

В следующей таблице представлено сравнение возможностей, доступных в EF Core и EF6. Это общее сравнение, здесь не указаны все функции и не объясняются различия между одной и той же возможностью в разных версиях EF.

Столбец EF Core содержит версию продукта, в которой эта возможность появилась впервые.

### <a name="creating-a-model"></a>Создание модели

| **Возможность**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Сопоставление базовых классов                                   | Да      | 1.0                                   |
| Конструкторы с параметрами                          |          | 2.1                                   |
| Преобразования значений свойств                            |          | 2.1                                   |
| Сопоставленные типы без ключей (типы запросов)                             |          | 2.1                                   |
| Соглашения                                           | Да      | 1.0                                   |
| Соглашения об именовании                                    | Да      | 1.0 (частично; [#214](https://github.com/dotnet/efcore/issues/214)) |
| Заметки к данным                                      | Да      | 1.0                                   |
| Текучий API                                            | Да      | 1.0                                   |
| Наследование: одна таблица на иерархию (TPH)                | Да      | 1.0                                   |
| Наследование: одна таблица на тип (TPT)                     | Да      | Запланировано на 5.0 ([#2266](https://github.com/dotnet/efcore/issues/2266)) |
| Наследование: одна таблица на конкретный класс (TPC)           | Да      | Stretch для 5.0 ([#3170](https://github.com/dotnet/efcore/issues/3170)) <sup>(1)</sup> |
| Теневые свойства состояния                               |          | 1.0                                   |
| Альтернативные ключи                                        |          | 1.0                                   |
| Навигация "многие ко многим"                              | Да      | Запланировано на 5.0 ([#19003](https://github.com/dotnet/efcore/issues/19003)) |
| Многие ко многим без сущности соединения                      | Да      | В журнале невыполненных работ ([#1368](https://github.com/dotnet/efcore/issues/1368)) |
| Создание ключей: База данных                              | Да      | 1.0                                   |
| Создание ключей: "Клиент";                                |          | 1.0                                   |
| Сложные и принадлежащие типы                                   | Да      | 2.0                                   |
| Пространственные данные                                          | Да      | 2.2                                   |
| Формат модели: Код                                    | Да      | 1.0                                   |
| Создание модели из базы данных: Командная строка              | Да      | 1.0                                   |
| Обновление модели из базы данных                            | Partial  | В журнале невыполненных работ ([#831](https://github.com/dotnet/efcore/issues/831)) |
| Глобальные фильтры запросов                                  |          | 2.0                                   |
| Разбиение таблиц                                       | Да      | 2.0                                   |
| Разбиение сущностей                                      | Да      | Stretch для 5.0 ([#620](https://github.com/dotnet/efcore/issues/620)) <sup>(1)</sup> |
| Сопоставление скалярных функций базы данных                      | Плохо     | 2.0                                   |
| Сопоставление полей                                         |          | 1.1                                   |
| Ссылочные типы, допускающие значение NULL (C# 8.0)                     |          | 3.0                                   |
| Графическое представление модели                      | Да      | Поддержка не запланирована <sup>(2)</sup>     |
| Графический редактор моделей                                | Да      | Поддержка не запланирована <sup>(2)</sup>     |
| Формат модели: EDMX (XML)                              | Да      | Поддержка не запланирована <sup>(2)</sup>     |
| Создание модели из базы данных: Мастер Visual Studio                 | Да      | Поддержка не запланирована <sup>(2)</sup>     |

### <a name="querying-data"></a>Запросы к данным

| **Возможность**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Запросы LINQ                                          | Да      | 1.0                                   |
| Создание удобного для чтения кода SQL                                | Плохо     | 1.0                                   |
| Преобразование оператора GroupBy                                   | Да      | 2.1                                   |
| Загрузка связанных данных: безотложная                           | Да      | 1.0                                   |
| Загрузка связанных данных: безотложная загрузка для производных типов |          | 2.1                                   |
| Загрузка связанных данных: Отложена                            | Да      | 2.1                                   |
| Загрузка связанных данных: Явные                        | Да      | 1.1                                   |
| Необработанные SQL-запросы: Типы сущностей                         | Да      | 1.0                                   |
| Необработанные SQL-запросы: Типы сущностей без ключей                 | Да      | 2.1                                   |
| Необработанные SQL-запросы: Создание с помощью LINQ                  |          | 1.0                                   |
| Явным образом скомпилированные запросы                           | Плохо     | 2.0                                   |
| await foreach (C# 8.0)                                |          | 3.0                                   |
| Язык текстовых запросов (Entity SQL)                | Да      | Поддержка не запланирована <sup>(2)</sup>     |

### <a name="saving-data"></a>Сохранение данных

| **Возможность**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Отслеживание изменений: Снимок                             | Да      | 1.0                                   |
| Отслеживание изменений: Уведомление                         | Да      | 1.0                                   |
| Отслеживание изменений: Прокси-элементы                              | Да      | Слияние для 5.0 ([#10949](https://github.com/dotnet/efcore/issues/10949)) |
| Доступ к отслеживаемому состоянию                               | Да      | 1.0                                   |
| Оптимистическая блокировка                                | Да      | 1.0                                   |
| Транзакции                                          | Да      | 1.0                                   |
| Пакетная обработка инструкций                                |          | 1.0                                   |
| Сопоставление хранимых процедур                              | Да      | В журнале невыполненных работ ([#245](https://github.com/dotnet/efcore/issues/245)) |
| Низкоуровневые API для несвязных графов                     | Плохо     | 1.0                                   |
| Полный проход несвязных графов                         |          | 1.0 (частично; [#5536](https://github.com/dotnet/efcore/issues/5536)) |

### <a name="other-features"></a>Другие возможности

| **Возможность**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Миграции                                            | Да      | 1.0                                   |
| Интерфейсы API для создания и удаления баз данных                       | Да      | 1.0                                   |
| Начальное значение данных                                             | Да      | 2.1                                   |
| Устойчивость подключений                                 | Да      | 1.1                                   |
| Перехватчики                                          | Да      | 3.0                                   |
| События                                                | Да      | 3.0 (частично; [#626](https://github.com/dotnet/efcore/issues/626)) |
| Простое ведение журналов (Database.Log)                         | Да      | Слияние для 5.0 ([#1199](https://github.com/dotnet/efcore/issues/1199)) |
| Создание пулов DbContext                                     |          | 2.0                                   |

### <a name="database-providers-sup3sup"></a>Поставщики баз данных <sup>(3)</sup>

| **Возможность**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| SQL Server                                            | Да      | 1.0                                   |
| MySQL                                                 | Да      | 1.0                                   |
| PostgreSQL                                            | Да      | 1.0                                   |
| Oracle                                                | Да      | 1.0                                   |
| SQLite                                                | Да      | 1.0                                   |
| SQL Server Compact                                    | Да      | 1.0 <sup>(4)</sup>                    |
| DB2                                                   | Да      | 1.0                                   |
| Firebird                                              | Да      | 2.0                                   |
| Jet (Microsoft Access)                                |          | 2.0 <sup>(4)</sup>                    |
| Azure Cosmos DB                                       |          | 3.0                                   |
| В памяти (для тестирования)                               |          | 1.0                                   |

<sup>1</sup> Цели Stretch, скорее всего, не будут достигнуты в данном выпуске. Однако, если все пойдет хорошо, мы постараемся добавить их.

<sup>2</sup> Некоторые функции EF6 не будут реализованы в EF Core. Эти функции зависят от базовых EDM EF6 и (или) являются сложными функциями с относительно низкой рентабельностью инвестиций. Мы всегда приветствуем обратную связь, но несмотря на то, что EF Core предоставляет многие функции, недоступные в EF6, для EF Core, наоборот, невозможно поддерживать все функции EF6.

<sup>3</sup> Поставщики баз данных EF Core, реализованные сторонними производителями, могут задерживаться при обновлении до новых основных версий EF Core. Дополнительные сведения можно найти в статье [Поставщики баз данных](../core/providers/index.md).

<sup>4</sup> Поставщики SQL Server Compact и Jet работают только в .NET Framework (но не в .NET Core).

### <a name="supported-platforms"></a>Поддерживаемые платформы

EF Core 3.1 работает на .NET Core и .NET Framework с помощью .NET Standard 2.0. Однако, EF Core 5.0 не будет работать в .NET Framework. Дополнительные сведения см. в разделе [Реализации .NET, поддерживаемые EF Core](../core/platforms/index.md).

EF6.4 выполняется в .NET Core и .NET Framework с помощью многоплатформенного нацеливания.

## <a name="guidance-for-new-applications"></a>Рекомендации для новых приложений

Используйте EF Core в .NET Core для всех новых приложений, если приложению не требуются компоненты, [поддерживаемые только в .NET Framework](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

## <a name="guidance-for-existing-ef6-applications"></a>Рекомендации для существующих приложений EF6

EF Core не является заменой для EF6. Для перехода с EF6 на EF Core, скорее всего, потребуется внести изменения в приложение.

При перемещении приложения EF6 в .NET Core.
* Продолжайте использовать EF6, если код доступа к данным стабилен и вряд ли будет развиваться или нуждаться в новых функциях.
* Выполняйте перенос в EF Core, если код доступа к данным развивается или приложению нужны новые функции, доступные только в EF Core.
* Перенос в EF Core также часто выполняется для повышения производительности. Однако не все сценарии работают быстрее, поэтому сначала сделайте профилирование.

Дополнительные сведения см. в разделе [Porting from EF6 to EF Core](porting/index.md) (Перенос приложений из EF6 в EF).
