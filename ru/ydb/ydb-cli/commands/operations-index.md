# Работа со вторичными индексами

В разделе описано как управлять вторичными индексами. Добавление вторичного индекса выполняется в асинхронном режиме. После запуска такой команды {{ ydb-short-name }} CLI сразу выдает информацию об операции и дает пользователю возможность продолжить работу.

## Добавление вторичного индекса {#operation-index}

Добавьте индекс `title_index` по колонкам `title` и `series`:

```bash
{{ ydb-cli }} table index add global \
  --index-name title_index \
  --columns title series
```

Результат:

```text
┌───────────────────────────────────────┬───────┬────────┐
| id                                    | ready | status |
├───────────────────────────────────────┼───────┼────────┤
| ydb://buildindex/7?id=562950460138467 | false |        |
└───────────────────────────────────────┴───────┴────────┘
```

Где `id` — идентификатор операции построения индекса.

## Получение информации о выполнении операции {#one-get}

Чтобы получить информацию о ходе добавления вторичного индекса, проверьте статус операции:

```bash
{{ ydb-cli }} operation get ydb://buildindex/7?id=562950460138467
```

Результат:

```bash
┌──────────────────────────────────┬───────┬─────────┬───────┬──────────┬────────────────────────────────┬─────────────┐
| id                               | ready | status  | state | progress | table                          | index       |
├──────────────────────────────────┼───────┼─────────┼───────┼──────────┼────────────────────────────────┼─────────────┤
| ydb://buildindex/7?id=5629504601 | true  | SUCCESS | Done  | 100.00%  | /ru-central1/b1gia87mbaomkfvsl | title_index |
| 38467                            |       |         |       |          | eds/etn02j1mlm4vgjhij03e/serie |             |
|                                  |       |         |       |          | s                              |             |
└──────────────────────────────────┴───────┴─────────┴───────┴──────────┴────────────────────────────────┴─────────────┘
```

Где:

* `progress` — процент шардов исходной таблицы, завершивших трансфер данных.
* `status` — Результат всей операции построения. Отображается, когда операция уже завершена.

## Получение информации обо всех операциях построения индекса для базы {#all-get}

Посмотрите все операции построения индекса для БД:

```bash
{{ ydb-cli }} operation list buildindex
```

Результат:

```text
┌─────────────────────────────────┬───────┬─────────┬───────┬──────────┬─────────────────────────────────┬─────────────┐
| id                              | ready | status  | state | progress | table                           | index       |
├─────────────────────────────────┼───────┼─────────┼───────┼──────────┼─────────────────────────────────┼─────────────┤
| ydb://buildindex/7?id=562950460 | true  | SUCCESS | Done  | 100.00%  | /ru-central1/b1gia87mbaomkfvsle | title_index |
| 138467                          |       |         |       |          | ds/etn02j1mlm4vgjhij03e/series  |             |
├─────────────────────────────────┼───────┼─────────┼───────┼──────────┼─────────────────────────────────┼─────────────┤
| ydb://buildindex/7?id=281475457 | true  | SUCCESS | Done  | 100.00%  | /ru-central1/b1gia87mbaomkfvsle | idname      |
| 674715                          |       |         |       |          | ds/etn02j1mlm4vgjhij03e/my_tabl |             |
|                                 |       |         |       |          | e                               |             |
└─────────────────────────────────┴───────┴─────────┴───────┴──────────┴─────────────────────────────────┴─────────────┘

Next page token: 0
```

## Отмена операции построения индекса {#cancel}

Отмените операцию построения индекса:

```bash
{{ ydb-cli }} operation cancel ydb://buildindex/7?id=562950460138467
```

После того как операция завершена или отменена, удалите ее из базы:

```bash
{{ ydb-cli }} operation forget ydb://buildindex/7?id=562950460138467
```

## Удаление индекса {#delete-index}

Если индекс не нужен, то его можно удалить. Удаление индекса — обычный синхронный запрос. Результат возвращается сразу по исполнению запроса.

Удалите индекс:

```bash
{{ ydb-cli }} table index drop --index-name title_index series
```
