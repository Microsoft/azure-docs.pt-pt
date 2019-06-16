---
title: Como resolver problemas de desempenho de consultas na base de dados do Azure para MySQL
description: Este artigo descreve como utilizar EXPLICATIVO para resolver problemas de desempenho de consulta na base de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 819e2393619766d46385cdd6fe550fff1e1a7631
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60720217"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Como utilizar EXPLICATIVO para desempenho de consulta de perfil na base de dados do Azure para MySQL
**EXPLIQUE** é uma ferramenta útil para otimizar as consultas. EXPLIQUE a instrução pode ser utilizada para obter informações sobre como as instruções SQL são executadas. O resultado seguinte mostra um exemplo da execução de uma instrução de EXPLICAR.

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

Como pode ser visto no exemplo, o valor de *chave* má hodnotu NULL. Esta saída significa MySQL não é possível localizar qualquer índice otimizado para a consulta e executa uma análise da tabela completa. Vamos otimizar esta consulta, adicione um índice no **ID** coluna.

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

O novo EXPLICATIVO mostra que o MySQL usa agora um índice para limitar o número de linhas a 1, que baixou por sua vez drasticamente o tempo de pesquisa.
 
## <a name="covering-index"></a>Índice abrangente
Um índice abrangente é composta por todas as colunas de uma consulta no índice para reduzir a obtenção de valor das tabelas de dados. Aqui está uma ilustração a seguir **GROUP BY** instrução.
 
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

Como pode ser visto a partir da saída, o MySQL não utiliza os índices porque não existem índices adequados estão disponíveis. Ela também mostra *usando temporário; Usando o tipo de ficheiro*, que significa que o MySQL cria uma tabela temporária para satisfazer os **GROUP BY** cláusula.
 
Criar um índice na coluna **c2** sozinho faz nenhuma diferença e MySQL ainda precisa para criar uma tabela temporária:

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

Neste caso, um **índice coberta** em ambos **c1** e **c2** podem ser criadas por meio das quais o valor de a adicionar **c2**"diretamente no índice para Elimine ainda mais a pesquisa de dados.

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

Como mostra a EXPLICAR acima, o MySQL é agora utiliza o índice coberto e evitar a criação de uma tabela temporária. 

## <a name="combined-index"></a>Índice combinada
Um índice combinado consiste em valores de várias colunas e pode ser considerado uma matriz de linhas que são ordenados pela concatenação valores das colunas indexadas. Este método pode ser útil para um **GROUP BY** instrução.

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

MySQL realiza uma *tipo de ficheiro* operação que está bastante lento, especialmente quando for que ordenar o número de linhas. Para otimizar esta consulta, um índice combinado pode ser criado em ambas as colunas que estão a ser classificadas.

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

A EXPLICAR agora mostra que a MySQL é capaz de usar o índice combinado para evitar a ordenação adicional, uma vez que o índice já esteja ordenado.
 
## <a name="conclusion"></a>Conclusão
 
Usando EXPLICATIVO e um tipo diferente de índices pode aumentar o desempenho significativamente. Só porque tem um índice tabela não significa necessariamente que MySQL seria capaz de usá-lo para as suas consultas. Sempre validar suas suposições usando EXPLICATIVO e otimizar as suas consultas com índices.


## <a name="next-steps"></a>Passos Seguintes
- Para encontrar respostas de ponto a ponto às suas perguntas mais atento ou postar uma nova pergunta/resposta, visite [fórum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
