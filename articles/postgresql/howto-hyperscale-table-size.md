---
title: Determinar o tamanho da tabela - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Como encontrar o verdadeiro tamanho das tabelas distribuídas num grupo de servidores Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937680"
---
# <a name="determine-table-and-relation-size"></a>Determinar o tamanho da tabela e da relação

A forma habitual de encontrar tamanhos de mesa em PostgreSQL, `pg_total_relation_size` subestima drasticamente o tamanho das tabelas distribuídas em Hyperscale (Citus).
Tudo o que esta função faz num grupo de servidores de Hiperescala (Citus) é revelar o tamanho das tabelas no nó coordenador.  Na realidade, os dados em tabelas distribuídas vivem nos nós dos trabalhadores (em cacos), não no coordenador. Uma verdadeira medida do tamanho da mesa distribuída é obtida como uma soma de tamanhos de fragmentos. A hiperescala (Citus) fornece funções de ajudante para consultar esta informação.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>Função</th>
<th>Devoluções</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size(relation_name)</td>
<td><ul>
<li>Tamanho dos dados reais na tabela (o "<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">garfo principal</a>").</li>
<li>Uma relação pode ser o nome de uma tabela ou de um índice.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size(relation_name)</td>
<td><ul>
<li><p>citus_relation_size mais:</p>
<blockquote>
<ul>
<li>tamanho do <a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">mapa de espaço livre</a></li>
<li>tamanho do mapa de <a href="https://www.postgresql.org/docs/current/static/storage-vm.html">visibilidade</a></li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size(relation_name)</td>
<td><ul>
<li><p>citus_table_size mais:</p>
<blockquote>
<ul>
<li>tamanho dos índices</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Estas funções são análogas a três das [funções](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)padrão de tamanho do objeto PostgreSQL , exceto se não conseguirem ligar-se a um nó, estão a errar.

## <a name="example"></a>Exemplo

Aqui está como listar os tamanhos de todas as tabelas distribuídas:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Resultado:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [escalar um grupo de servidor](howto-hyperscale-scale-grow.md) para conter mais dados.
* Distinguir [tipos de mesa](concepts-hyperscale-nodes.md) num grupo de servidores Hyperscale (Citus).
