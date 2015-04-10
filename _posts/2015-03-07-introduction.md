---
layout: post
title:  "Вводная лекция"
categories: NoSQL, BigData
date:   2015-03-07
---

[Ссылка на презентацию][presentation].

#Краткий конспект вводной лекции

Первая лекция будет состоять из четырех блоков:

1. [Программа курса](#syllabus)
2. [История](#history)
3. [Основные понятия](#basic_concepts)
4. [CAP-теорема](#cap)
5. [Полезные ссылки](#links)

###<a name="syllabus" />Syllabus

* В рамках курса мы поговорим о "кирпичиках", из которых строятся распределенные системы, и о проблемах, которые приходится решать при создании этих самых распределенных систем.
* Курс семестровый.
* Читать его вам будут [Артем Зиненко](mailto:art@skbkontur.ru) и [Саша Коковин](mailto:kokovin@skbkontur.ru) (мы разделились примерно пополам).
* Курс состоит из теорий и практик.

####Themes
1. [LSM-Tree (Log-Structured Merge-Tree)](http://en.wikipedia.org/wiki/Log-structured_merge-tree) - специальная структура данных, которая позволяет добиться очень высокой скорости записи. Есть внутри большого количества баз данных: например, [Cassandra](http://cassandra.apache.org).
2. [B-tree](http://en.wikipedia.org/wiki/B-tree) - специальная структура данных, которая оптимизирована для чтения/записи больших блоков данных (базы данных). Эта структура данных обычно лежит в основе вторичных индексов.
3. [BSON (Binary JavaScript Object Notation)](https://en.wikipedia.org/wiki/BSON) - надмножество [JSON](https://en.wikipedia.org/wiki/JSON) (дополнительно включает регулярные выражения, двоичные данные, даты), бинарная форма представления [JSON](https://en.wikipedia.org/wiki/JSON). Эффективнее [JSON](https://en.wikipedia.org/wiki/JSON) в плане хранения данных и чтения с диска. Аналог [Protocol Buffers](http://en.wikipedia.org/wiki/Protocol_Buffers).
4. [Replication (Data Replication)](http://en.wikipedia.org/wiki/Replication_(computing)) - процесс копирования данных. Используется для повышения надежности хранения данных. Может использоваться и для повышения пропускной способности (например, на чтение): [RAID 1](https://ru.wikipedia.org/wiki/RAID#RAID_1).
5. [Sharding](http://en.wikipedia.org/wiki/Shard_(database_architecture)) - основа горизонтального масштабирования - разделение целого на части. Позволяет распределять данные по большому количеству серверов (например, когда данных ОЧЕНЬ много).
6. <a name="strong_consistency" />[Consistency (строгая)](http://en.wikipedia.org/wiki/Strong_consistency) - все клиенты видят одно и то же (либо все видят старые данные, либо новые). Изменения могут занимать очень много времени. [Транзакции](http://en.wikipedia.org/wiki/Distributed_transaction) ("все или ничего"). Возможны [deadlock](http://en.wikipedia.org/wiki/Deadlock)-и.
7. [Eventual Consistency](http://en.wikipedia.org/wiki/Eventual_consistency)
    * Нужно обеспечивать высокую доступность на чтение.
    * Еще как-то нужно обеспечивать обновление данных (запись).
    * Пример: Пользователь обновляет свой статус. Что увидят его друзья? Кто увидит старый статус, кто новый?
    * NoSQL: Для больших приложений нельзя ждать очень долго (можем вообще не дождаться, если какая-то машина выключилась). Компромис: В нашей ситуации, в общем-то неважно, если кто-то из пользователей увидит старые данные.
    * В отсутствии новых операций записи все реплики сходятся к идентичным копиям данных (то, что приложение видит до момента сходимости, зависит от конкретного механизма репликации и сложно предсказуемо).
8. [ACID (Atomicity, Consistency, Isolation, Durability)](https://en.wikipedia.org/wiki/ACID)
    * [Atomicity](https://en.wikipedia.org/wiki/ACID#Atomicity) - никакая транзакция не будет зафиксирована в системе частично: либо будут выполнены все ее операции, либо ни одной. Если транзакцию завершить не удается, то все выполненные операции откатываются (rollback).
    * [Consistency](https://en.wikipedia.org/wiki/ACID#Consistency) - согласованность (непротиворечивость) данных. Каждая транзакция фиксирует только допустимые результаты. Отличается от [строгой консистентности](#strong_consistency) тем, что там нет требования непритиворечивости, достаточно, чтобы все клиенты видели одинаковый набор данных. Пример: нужно провести перевод денежных средств с одного банковского счета на другой.
    * [Isolation](https://en.wikipedia.org/wiki/ACID#Isolation) - транзакции не должны влиять на результат других транзакций.
    * [Durability](https://en.wikipedia.org/wiki/ACID#Durability) - в случае каких-то сбоев изменения, сделанные успешно завершенной транзакцией, не исчезнут.
9. [Oplog](http://en.wikipedia.org/wiki/Intent_log) - функция [базы данных](http://en.wikipedia.org/wiki/Database), которая позволяет сохранять информацию, необходимую для восстановления системы в предыдущее согласованное состояние в случае сбоев.
10. [Vector clock](http://en.wikipedia.org/wiki/Vector_clock) - алгоритм генерации частично упорядоченных событий в распределенной системе. Нужен, чтобы упорядочивать операции, разрешать конфликты.
11. [Paxos](http://en.wikipedia.org/wiki/Paxos_(computer_science)), [Raft](http://en.wikipedia.org/wiki/Raft_(computer_science)) - алгоритмы достижения консенсусса (согласия) в распределенной системе среди множества участников. [Raft](http://en.wikipedia.org/wiki/Raft_(computer_science)) - более понятный.
12. [MapReduce](http://en.wikipedia.org/wiki/MapReduce) - модель распределенных вычислений, используемая для паралелльных вычислений над очень большими объемами данных (петабайты).
13. [Persistent data structure](http://en.wikipedia.org/wiki/Persistent_data_structure) - структура данных, которая при модификации сохраняет свое предыдущее состояние. [Partially persistent](http://en.wikipedia.org/wiki/Persistent_data_structure#Partially_persistent) - ко всем версиям можно получить доступ, но менять можно только последнюю. [Fully Persistent](http://en.wikipedia.org/wiki/Persistent_data_structure#Fully_persistent) - менять можно все версии.
14. [Message Queue](http://en.wikipedia.org/wiki/Message_queue) - компонент, используемый для [IPC](http://en.wikipedia.org/wiki/Inter-process_communication). Протокол асинхронного взаимодействия: sender и receiver не обязаны взаимодействовать с очередью одновременно.
15. [MMAP](http://en.wikipedia.org/wiki/Memory-mapped_file) - сегмент виртуальной памяти, ассоциированный с файлом. Все операции с файлом - операции с памятью. Увеличивает производительность ввода-вывода (особенно для больших файлов).

####Assessments

Нужно будет уметь программировать

Задачи:

* Bloom Filters
* Replication
* Sharding
* Consistency
* Map Reduce
* Vector clock

Подробнее про каждую из задач расскажем, когда будем ее выдавать (это будет, скорее всего, на следующей лекции).

###<a name="history" />История
* SQL
  * Данные перестали помещаться в оперативную память (сделали индексы).
  * Объем данных перестал помещаться на одну машину.
  * [10K Problem](https://en.wikipedia.org/wiki/C10k_problem).
  * Захотелось [Fault tolerance](http://en.wikipedia.org/wiki/Fault_tolerance).
  * Schema-on-Read - очень много (сотни ТБ) разнородных данных, их очень сложно "уложить" в какую-то схему (она только мешает). Для работы с такими данными начали использовать MapReduce.
* NoSQL (начало 2000, Google)
  * Возможность горизонтально масштабировать "простые" операции по большому количеству серверов. Примеры "простых" операций: key lookup, read/write одной или небольшого количества записей.
  * Простое API - нет языка запросов.
  * Concurrency Model слабее, чем [ACID](https://en.wikipedia.org/wiki/ACID) ([ACID](https://en.wikipedia.org/wiki/ACID) в распределенной системе сложно добиться).
  * Эффективное использование распределенных индексов и оперативной памяти для хранения данных.
  * Возможность добавлять новые атрибуты к записям (гибкая структура).
  * [Memcached](http://en.wikipedia.org/wiki/Memcached) показал, что in-memory индексы могут быть хорошо масштабируемы (легко можно сделать sharding и replication).
  * [Amazon Dynamo](http://en.wikipedia.org/wiki/Dynamo_(storage_system)) - показали, что идея использования [Eventual Consistency](http://en.wikipedia.org/wiki/Eventual_consistency) - хороший способ достичь высокой доступности и масштабируемости: не гарантируется, что прочитанные данные самые новые, но гарантируется, что в конечном счете записи распространятся на все узлы.
  * [BigTable](http://en.wikipedia.org/wiki/BigTable) (2006) - показал, что persistent storage может быть масштабирован на тысячи узлов.
* NewSQL (2011)
  * [VoltDB](http://en.wikipedia.org/wiki/VoltDB), [MemSQL](http://en.wikipedia.org/wiki/MemSQL), [Spanner](https://en.wikipedia.org/wiki/Spanner_(database)).
  * Пытаются совместить преимущества NoSQL баз данных и требования транзакционности.
  * Потребности в основном у компаний из финансового сектора (NoSQL не отвечает требованиям надежности данных).

###<a name="basic_concepts" />Основные понятия

####Schema
Это общее соглашение об устройстве "мира". Как правило, данные "in the wild" обычно ему не удовлетворяют.

Schema-later: схема важна, но не обязательна для записи данных. Это хорошо подходит для Data Science (MapReduce), для высоконагруженных приложений уже не очень (в приложении схема все равно есть).

####Big Data
* Volume - размер данных (число строк, объектов, байт).
* Variety - разнообразие (число колонок, измерений, форматов)
* Velocity - скорость (число строк/байт, обрабатываемое в единицу времени): задержка обработки данных по отношению к растущей потребности в realtime-е.

Big Data - "Difficult Data" - любые данные, которыми дорого управлять и тяжело извлекать из них пользу. Big Data с технической точки зрения - объемы дисков растут, а скорость их работы - нет.

####Data Model
* Structures
  * строки и колонки
  * узлы и ребра
  * ключи и значения
  * последовательность байт
* Constraints
  * У всех строк должен быть одинаковый набор колонок
  * Все значения в одной колонке должны иметь одинаковый тип
  * Значения в колонке должны быть в определенном диапазоне
  * Дочерний узел не может иметь два родительских узла
* Operations (операции, которые эти структуры поддерживают)
  * Найди значение по ключу X
  * Найди строки, где значение колонки lastname='Bob'
  * Дай следующие N байт (или M байт по определенному смещению)

####База данных
База данных - набор информации, организованной с целью эффективного доступа к ней (очень общее определение, нет ни таблиц, ни отношений).
Сейчас термин База Данных часто употребляют, когда говорят о данных + схеме.

Зачем нужны базы данных? Какую проблему они решают?

* Sharing - конкурентный доступ к данным многих читателей/писателей.
* Data Model Enforcement - все приложения видят организованные данные (строки, колонки).
* Масштабирование - объемы данных очень большие, чтобы влезать в память/диск, а БД могут как-то организовать работу с данными.
* Flexibility - гибкость в использовании данных.
  * Подход RDBMS: сначала придумываем "схему", по ней "раскладываем" данные определенным образом (нормализуем их), нормализованные данные позволяют более-менее эффективно решить нужную задачу.
  * Подход MapReduce: как-то складываем данные, потом пишем код, чтобы этими данными как-то воспользоваться (не факт что получится эффективно).
  * Подход NoSQL: сначала понимаем, какие операции с данными нужно уметь выполнять, после этого "раскладываем" данные так, чтобы операции можно было эффективно выполнить (идем от задачи, а не от данных).


###<a name="cap" />CAP
* Consistency - видят ли все приложения одинаковый набор данных
* Availability - могу ли я взаимодействовать с системой, когда в ее части происходят сбои?
* Partitioning (Partition Tolerance) - две части системы не могут общаться друг с другом, могут ли они существовать сами по себе:
  * Да - Availability
  * Нет - Consistency (Stop-and-Wait).

Теорема - можно выбрать только 2 из 3.

###<a name="links" />Полезные ссылки
* [Seven Databases in Seven Weeks: A Guide to Modern Databases and the NoSQL Movement by Eric Redmond and Jim R. Wilson](https://pragprog.com/book/rwdata/seven-databases-in-seven-weeks) - отличная обзорная книга по существующим базам данных.
* [Онлайн-курсы по MongoDB](https://university.mongodb.com) - если пользуетесь или собираетесь ей пользоваться, то очень рекомендую.
* Курс [Introduction to Databases](https://www.coursera.org/course/db) от Stanford - неплохой вводный курс.
* Курс [Introduction to Data Science](https://class.coursera.org/datasci-001) - там есть немного про NoSQL и MapReduce.


[presentation]: {{site.baseurl}}/content/Introduction.pdf