---
title: Desempenho da consulta de resolução de problemas - Base de Dados Azure para MariaDB
description: Aprenda a usar o EXPLAIN para resolver problemas de desempenho na Base de Dados Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 2b7491723ffcff73e4b243fe54ef18608167d636
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664720"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Como utilizar o EXPLAIN para o desempenho da consulta de perfil na Base de Dados Azure para a MariaDB
**O EXPLAIN** é uma ferramenta útil para otimizar as consultas. A declaração de EXPLICAÇÃO pode ser usada para obter informações sobre como as declarações sql são executadas. A seguinte saída mostra um exemplo da execução de uma declaração EXPLICADA.

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

Como se pode ver a partir deste exemplo, o valor da *chave* é NU. Esta saída significa que a MariaDB não consegue encontrar quaisquer índices otimizados para a consulta e realiza uma verificação completa da tabela. Vamos otimizar esta consulta adicionando um índice na coluna **ID.**

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

O novo EXPLAIN mostra que a MariaDB usa agora um índice para limitar o número de linhas a 1, o que por sua vez encurtou drasticamente o tempo de pesquisa.
 
## <a name="covering-index"></a>Índice de cobertura
Um índice de cobertura consiste em todas as colunas de uma consulta no índice para reduzir a recuperação de valor das tabelas de dados. Aqui está uma ilustração no seguinte **comunicado do GRUPO BY.**
 
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

Como se pode ver pela produção, a MariaDB não utiliza nenhum índice porque não existem índices adequados. Também mostra *a utilização temporária; Utilizando o tipo de ficheiro*, o que significa que o MariaDB cria uma tabela temporária para satisfazer a cláusula GROUP **BY.**
 
Criar um índice só na coluna **c2** não faz diferença, e MariaDB ainda precisa de criar uma tabela temporária:

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

Neste caso, pode ser criado um **índice coberto** tanto em **c1** como **em c2,** acrescentando o valor de **c2**" diretamente no índice para eliminar mais análises de dados.

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

Como mostra o EXPLAIN acima, a MariaDB usa agora o índice coberto e evita a criação de uma tabela temporária. 

## <a name="combined-index"></a>Índice combinado
Um índice combinado consiste em valores de várias colunas e pode ser considerado um conjunto de linhas que são classificadas por valores concatenantes das colunas indexadas. Este método pode ser útil numa declaração **do GRUPO BY.**

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

A MariaDB realiza uma operação *de classificação de ficheiros* bastante lenta, especialmente quando tem de separar muitas linhas. Para otimizar esta consulta, um índice combinado pode ser criado em ambas as colunas que estão sendo classificadas.

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

O EXPLAIN mostra agora que o MariaDB é capaz de utilizar o índice combinado para evitar uma triagem adicional, uma vez que o índice já está classificado.
 
## <a name="conclusion"></a>Conclusão
 
A utilização de EXPLAIN e diferentes tipos de Índices pode aumentar significativamente o desempenho. Ter um índice em cima da mesa não significa necessariamente que a MariaDB possa usá-lo para as suas consultas. Valide sempre os seus pressupostos utilizando o EXPLAIN e otimize as suas consultas utilizando índices.

## <a name="next-steps"></a>Passos seguintes
- Para encontrar respostas de pares às suas perguntas mais preocupadas ou publicar uma nova pergunta/resposta, visite [o Microsoft Q&Uma página de perguntas](/answers/topics/azure-database-mariadb.html) ou Stack [Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).