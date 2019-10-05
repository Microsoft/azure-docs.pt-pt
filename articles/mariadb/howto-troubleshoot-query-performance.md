---
title: Solucionar problemas de desempenho de consulta no banco de dados do Azure para MariaDB
description: Saiba como usar explicar para solucionar problemas de desempenho de consulta no banco de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 11/09/2018
ms.openlocfilehash: a2f5e7e7c9ca39c092e13242ecdac2675b09fc0d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973509"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Como usar explicar para criar um perfil de desempenho de consulta no banco de dados do Azure para MariaDB
A **explicação** é uma ferramenta útil para otimizar consultas. A instrução explicar pode ser usada para obter informações sobre como as instruções SQL são executadas. A saída a seguir mostra um exemplo da execução de uma instrução de explicação.

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

Como pode ser visto neste exemplo, o valor da *chave* é NULL. Essa saída significa que MariaDB não pode localizar nenhum índice otimizado para a consulta e executa uma verificação de tabela completa. Vamos otimizar essa consulta adicionando um índice na coluna **ID** .

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

O novo explicação mostra que o MariaDB agora usa um índice para limitar o número de linhas a 1, o que, por sua vez, reduziu drasticamente o tempo de pesquisa.
 
## <a name="covering-index"></a>Índice de cobertura
Um índice abrangente consiste em todas as colunas de uma consulta no índice para reduzir a recuperação de valor de tabelas de dados. Aqui está uma ilustração na instrução **Group by** a seguir.
 
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

Como pode ser visto na saída, MariaDB não usa nenhum índice porque nenhum índice adequado está disponível. Ele também mostra o *uso de temporário; Usando a classificação de arquivos*, o que significa que o MariaDB cria uma tabela temporária para satisfazer a cláusula **Group by** .
 
Criar um índice na coluna **C2** sozinho não faz diferença e MariaDB ainda precisa criar uma tabela temporária:

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

Nesse caso, um **índice coberto** em **C1** e **C2** pode ser criado, por meio da adição do valor de **C2**"diretamente no índice para eliminar a pesquisa de dados adicional.

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

Como mostra a explicação acima, o MariaDB agora usa o índice coberto e evita a criação de uma tabela temporária. 

## <a name="combined-index"></a>Índice combinado
Um índice combinado consiste em valores de várias colunas e pode ser considerado uma matriz de linhas que são classificadas por meio da concatenação de valores das colunas indexadas. Esse método pode ser útil em uma instrução **Group by** .

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

O MariaDB executa uma operação de *classificação de arquivo* que é razoavelmente lenta, especialmente quando precisa classificar várias linhas. Para otimizar essa consulta, um índice combinado pode ser criado em ambas as colunas que estão sendo classificadas.

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

A explicação agora mostra que o MariaDB é capaz de usar o índice combinado para evitar classificação adicional, pois o índice já está classificado.
 
## <a name="conclusion"></a>Conclusão
 
O uso de EXPLICAções e tipos diferentes de índices pode aumentar significativamente o desempenho. Ter um índice na tabela não significa necessariamente que MariaDB seria capaz de usá-lo para suas consultas. Sempre valide suas suposições usando explicar e otimizar suas consultas usando índices.

## <a name="next-steps"></a>Passos seguintes
- Para encontrar respostas de pares para suas perguntas mais interessadas ou postar uma nova pergunta/resposta, visite o [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDatabaseforMariadb) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).
