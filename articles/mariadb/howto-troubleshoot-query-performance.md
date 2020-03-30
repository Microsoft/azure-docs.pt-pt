---
title: Performance de consulta de resolução de problemas - Base de Dados Azure para MariaDB
description: Saiba como usar o EXPLAIN para resolver o desempenho da consulta na Base de Dados Azure para o MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: b06fe37b63494eb4ee0ca680733a801c26415d67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530058"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Como usar o EXPLAIN para perfilar o desempenho na Base de Dados Azure para MariaDB
**Explicar** é uma ferramenta útil para otimizar consultas. A declaração de EXPLICAÇÃO pode ser usada para obter informações sobre como as declarações do SQL são executadas. A saída seguinte mostra um exemplo da execução de uma declaração de EXPLICA.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Como se pode ver a partir deste exemplo, o valor da *chave* é NULO. Esta saída significa que a MariaDB não encontra quaisquer índices otimizados para a consulta e realiza uma digitalização completa da tabela. Vamos otimizar esta consulta adicionando um índice na coluna **de identificação.**

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

O novo EXPLAIN mostra que mariaDB usa agora um índice para limitar o número de linhas a 1, o que por sua vez encurtou drasticamente o tempo de pesquisa.
 
## <a name="covering-index"></a>Índice de cobertura
Um índice de cobertura consiste em todas as colunas de uma consulta no índice para reduzir a recuperação de valor a partir de tabelas de dados. Aqui está uma ilustração no seguinte **comunicado do GRUPO BY.**
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Como se pode ver pela saída, a MariaDB não utiliza quaisquer índices porque não existem índices adequados. Também mostra *usar temporário; Utilizando o tipo de ficheiro*, o que significa que a MariaDB cria uma tabela temporária para satisfazer a cláusula GROUP **BY.**
 
A criação de um índice só na coluna **c2** não faz diferença, e a MariaDB ainda precisa de criar uma tabela temporária:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Neste caso, pode ser criado um **índice coberto** tanto em **c1** como **c2,** pelo que a adição do valor de **c2**" diretamente no índice para eliminar a procura de dados adicionais.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Como mostra o explicador acima, a MariaDB usa agora o índice coberto e evita criar uma tabela temporária. 

## <a name="combined-index"></a>Índice combinado
Um índice combinado consiste em valores de várias colunas e pode ser considerado um conjunto de linhas que são ordenadas por valores concatenantes das colunas indexadas.Este método pode ser útil numa declaração do **GROUP BY.**

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MariaDB executa uma operação de *tipo de arquivo* que é bastante lenta, especialmente quando tem que ordenar muitas linhas. Para otimizar esta consulta, pode ser criado um índice combinado em ambas as colunas que estão a ser ordenadas.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

O EXPLAIN mostra agora que o MariaDB é capaz de usar o índice combinado para evitar a triagem adicional, uma vez que o índice já está classificado.
 
## <a name="conclusion"></a>Conclusão
 
A utilização de EXPLAIN e de diferentes tipos de Índices pode aumentar significativamente o desempenho. Ter um índice em cima da mesa não significa necessariamente que mariaDB seria capaz de usá-lo para as suas consultas. Valide sempre os seus pressupostos usando o EXPLAIN e otimize as suas consultas utilizando índices.

## <a name="next-steps"></a>Passos seguintes
- Para encontrar respostas dos pares às suas perguntas mais preocupadas ou publicar uma nova pergunta/resposta, visite o [fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDatabaseforMariadb) ou [stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).
