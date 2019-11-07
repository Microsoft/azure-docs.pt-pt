---
title: Ajuste de desempenho com exibições materializadas
description: Recomendações e considerações que você deve saber ao usar exibições materializadas para melhorar o desempenho da consulta.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: c1cfd3b4c365a04c3d4704f37e4ed4177fa74619
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692979"
---
# <a name="performance-tuning-with-materialized-views"></a>Ajuste de desempenho com exibições materializadas 
As exibições materializadas no Azure SQL Data Warehouse fornecem um método de baixa manutenção para consultas analíticas complexas para obter um desempenho rápido sem nenhuma alteração de consulta. Este artigo discute as diretrizes gerais sobre como usar exibições materializadas.


## <a name="materialized-views-vs-standard-views"></a>Exibições materializadas versus exibições padrão
O Azure SQL Data Warehouse dá suporte a exibições padrão e materializadas.  Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas a consultas como tabelas lógicas.  As exibições encapsulam a complexidade da computação de dados comum e adicionam uma camada de abstração às alterações de computação para que não seja necessário reescrever consultas.  

Uma exibição padrão calcula seus dados a cada vez que a exibição é usada.  Não há dados armazenados em disco. Normalmente, as pessoas usam modos de exibição padrão como uma ferramenta que ajuda a organizar os objetos lógicos e as consultas em um banco de dados.  Para usar um modo de exibição padrão, uma consulta precisa fazer referência direta a ele. 

Uma exibição materializada computa previamente, armazena e mantém seus dados no Azure SQL Data Warehouse assim como uma tabela.  Não há necessidade de recomputação toda vez que uma exibição materializada é usada.  É por isso que as consultas que usam todos ou subconjunto dos dados em exibições materializadas podem obter um desempenho mais rápido.  Melhor ainda, as consultas podem usar uma exibição materializada sem fazer referência direta a ela, portanto, não há necessidade de alterar o código do aplicativo.  

A maioria dos requisitos em uma exibição padrão ainda se aplica a uma exibição materializada. Para obter detalhes sobre a sintaxe de exibição materializada e outros requisitos, consulte [criar exibição materializada como SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).



| Comparação                     | Ver                                         | Vista materializada             
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------| 
|Ver definição                 | Armazenados no Azure data warehouse.              | Armazenados no Azure data warehouse.    
|Exibir conteúdo                    | Gerado toda vez que o modo de exibição é usado.   | Pré-processado e armazenado no Azure data warehouse durante a criação do modo de exibição. Atualizado à medida que os dados são adicionados às tabelas subjacentes.                                             
|Atualização de dados                    | Sempre atualizado                               | Sempre atualizado                          
|Velocidade para recuperar dados de exibição de consultas complexas     | Prejudicar                                         | Rápido  
|Armazenamento extra                   | Não                                           | Sim                             
|Sintaxe                          | CRIAR MODO DE EXIBIÇÃO                                  | CRIAR EXIBIÇÃO MATERIALIZADA COMO SELECT           
     
## <a name="benefits-of-using-materialized-views"></a>Benefícios do uso de exibições materializadas

Uma exibição materializada adequadamente projetada pode fornecer os seguintes benefícios:

- Reduza o tempo de execução para consultas complexas com junções e funções de agregação. Quanto mais complexa for a consulta, maior será o potencial para o salvamento do tempo de execução. A maior vantagem é obtida quando o custo de computação de uma consulta é alto e o conjunto de dados resultante é pequeno.  

- O otimizador no Azure SQL Data Warehouse pode usar automaticamente exibições materializadas implantadas para melhorar os planos de execução de consulta.  Esse processo é transparente para os usuários que fornecem um desempenho de consulta mais rápido e não requer consultas para fazer referência direta às exibições materializadas. 

- Exigir baixa manutenção nas exibições.  Todas as alterações de dados incrementais das tabelas base são automaticamente adicionadas às exibições materializadas de maneira síncrona.  Esse design permite consultar exibições materializadas para retornar os mesmos dados como consultando diretamente as tabelas base. 
- Os dados em uma exibição materializada podem ser distribuídos de forma diferente das tabelas base.  
- Os dados em exibições materializadas têm os mesmos benefícios de alta disponibilidade e resiliência que os dados em tabelas regulares.  
 
Comparando com outros provedores de data warehouse, as exibições materializadas implementadas no Azure SQL Data Warehouse também fornecem os seguintes benefícios adicionais: 

- Atualização automática e síncrona de dados com alterações de dados em tabelas base. Nenhuma ação do usuário é necessária. 
- Amplo suporte a funções de agregação. Consulte [criar exibição materializada como SELECT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).
- O suporte para a recomendação de exibição materializada específica de consulta.  Consulte [explicar (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Cenários comuns  

Exibições materializadas normalmente são usadas nos seguintes cenários: 

**Necessidade de melhorar o desempenho de consultas analíticas complexas em tamanho de dados grandes**

Consultas analíticas complexas normalmente usam mais funções de agregação e junções de tabela, causando mais operações de computação pesada, como embaralhamentos e junções na execução da consulta.  É por isso que essas consultas levam mais tempo para serem concluídas, especialmente em tabelas grandes.  Os usuários podem criar exibições materializadas para os dados retornados dos cálculos comuns de consultas, de modo que não há necessidade de recálculo quando esses dados são necessários por consultas, o que permite menor custo de computação e resposta de consulta mais rápida. 

**Necessidade de desempenho mais rápido, sem alterações de consulta mínimas**

Alterações de esquema e consulta em data warehouses normalmente são mantidas em um mínimo para dar suporte a operações e relatórios regulares de ETL.  As pessoas podem usar exibições materializadas para ajuste de desempenho de consulta, se o custo incorrido pelas exibições puder ser deslocado pelo lucro no desempenho da consulta. Em comparação com outras opções de ajuste, como gerenciamento de estatísticas e dimensionamento, trata-se de uma alteração de produção muito menos afetada para criar e manter uma exibição materializada e seu potencial de desempenho também é maior.

- Criar ou manter exibições materializadas não afeta as consultas em execução nas tabelas base.
- O otimizador de consulta pode usar automaticamente as exibições materializadas implantadas sem referência direta de exibição em uma consulta. Esse recurso reduz a necessidade de alteração de consulta no ajuste de desempenho. 

**Necessidade de uma estratégia de distribuição de dados diferente para um desempenho de consulta mais rápido**

O Azure data warehouse é um sistema MPP (processamento paralelo maciço) distribuído.   Os dados em uma tabela de data warehouse são distribuídos entre 60 nós usando uma das três [estratégias de distribuição](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute) (hash, round_robin ou replicadas).  A distribuição de dados é especificada no momento da criação da tabela e permanece inalterada até que a tabela seja descartada. A exibição materializada, sendo uma tabela virtual em disco, dá suporte a distribuições de dados de hash e round_robin.  Os usuários podem escolher uma distribuição de dados diferente das tabelas base, mas ideal para o desempenho de consultas que usam as exibições mais.  

## <a name="design-guidance"></a>Diretrizes de design 

Aqui está a orientação geral sobre como usar exibições materializadas para melhorar o desempenho da consulta:

**Design para sua carga de trabalho**

Antes de começar a criar exibições materializadas, é importante ter uma compreensão profunda de sua carga de trabalho em termos de padrões de consulta, importância, frequência e tamanho dos dados resultantes.  

Os usuários podem executar explicar WITH_RECOMMENDATIONS < SQL_statement > para as exibições materializadas recomendadas pelo otimizador de consulta.  Como essas recomendações são específicas de consulta, uma exibição materializada que beneficia uma única consulta pode não ser ideal para outras consultas na mesma carga de trabalho.  Avalie essas recomendações com suas necessidades de carga de trabalho em mente.  As exibições materializadas ideais são aquelas que beneficiam o desempenho da carga de trabalho.  

**Esteja atento à compensação entre consultas mais rápidas e o custo** 

Para cada exibição materializada, há um custo de armazenamento de dados e um custo para manter a exibição.  À medida que os dados são alterados nas tabelas base, o tamanho da exibição materializada aumenta e sua estrutura física também é alterada.  Para evitar a degradação do desempenho da consulta, cada exibição materializada é mantida separadamente pelo mecanismo de data warehouse.  A carga de trabalho de manutenção fica mais alta quando o número de exibições materializadas e as alterações na tabela base aumentam.   Os usuários devem verificar se o custo incorrido de todas as exibições materializadas pode ser deslocado pelo lucro de desempenho da consulta.  

Você pode executar essa consulta para a lista de exibições materializadas em um banco de dados: 

```sql
SELECT V.name as materialized_view, V.object_id 
FROM sys.views V 
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
``` 

Opções para reduzir o número de exibições materializadas: 

- Identifique conjuntos de dados comuns frequentemente usados pelas consultas complexas em sua carga de trabalho.  Crie exibições materializadas para armazenar esses conjuntos de dados para que o otimizador possa usá-los como blocos de construção ao criar planos de execução.  

- Descarte as exibições materializadas com baixo uso ou que não são mais necessárias.  Uma exibição materializada desabilitada não é mantida, mas ainda incorre em custo de armazenamento.  

- Combine exibições materializadas criadas nas mesmas ou em tabelas base semelhantes, mesmo que seus dados não se sobreponham.  A combinação de exibições materializadas pode resultar em uma exibição maior do que a soma das exibições separadas, no entanto, o custo da manutenção do modo de exibição deve ser reduzido.  Por exemplo:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Nem todo ajuste de desempenho requer alteração de consulta**

O otimizador de data warehouse pode usar automaticamente exibições materializadas implantadas para melhorar o desempenho da consulta.  Esse suporte é aplicado de forma transparente a consultas que não fazem referência às exibições e consultas que usam agregações sem suporte na criação de exibições materializadas.  Nenhuma alteração de consulta é necessária. Você pode verificar o plano de execução estimado de uma consulta para confirmar se uma exibição materializada é usada.  

**Monitorar exibições materializadas** 

Uma exibição materializada é armazenada na data warehouse assim como uma tabela com o CCI (índice columnstore clusterizado).  A leitura de dados de uma exibição materializada inclui a verificação dos segmentos do índice CCI e a aplicação de quaisquer alterações incrementais das tabelas base. Quando o número de alterações incrementais é muito alto, resolver uma consulta de uma exibição materializada pode levar mais tempo do que consultar diretamente as tabelas base.  Para evitar a degradação do desempenho da consulta, é uma boa prática executar [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest) para monitorar o overhead_ratio da exibição (total_rows/Max (1 base_view_row)).  Os usuários devem recriar a exibição materializada se sua overhead_ratio estiver muito alta. 

**Exibição materializada e cache de conjunto de resultados**

Esses dois recursos são introduzidos no Azure SQL Data Warehouse ao mesmo tempo para o ajuste de desempenho de consulta.  O cache do conjunto de resultados é usado para obter alta simultaneidade e resposta rápida de consultas repetitivas em dados estáticos.  Para usar o resultado em cache, a forma da consulta de solicitação de cache deve corresponder à consulta que produziu o cache.  Além disso, o resultado em cache deve se aplicar à consulta inteira.  Exibições materializadas permitem alterações de dados nas tabelas base.  Os dados em exibições materializadas podem ser aplicados a uma parte de uma consulta.  Esse suporte permite que as mesmas exibições materializadas sejam usadas por consultas diferentes que compartilham algum cálculo para um desempenho mais rápido.

## <a name="example"></a>Exemplo

Este exemplo usa uma consulta TPCDS que localiza os clientes que gastam mais dinheiro por meio do catálogo do que em lojas, identificam os clientes preferenciais e seu país de origem.   A consulta envolve a seleção dos principais registros 100 da União de três instruções de subseleção envolvendo SUM () e GROUP BY. 

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales 
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales 
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales 
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100 
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Verifique o plano de execução estimado da consulta.  Há 18 realeatórias e 17 operações de junções, que levam mais tempo para serem executadas. Agora, vamos criar uma exibição materializada para cada uma das três instruções de subseleção.   

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```
Verifique o plano de execução da consulta original novamente.  Agora, o número de junções muda de 17 para 5 e não há mais ordem aleatória.  Clique no ícone de operação de filtro no plano. sua lista de saída mostra que os dados são lidos nas exibições materializadas em vez de tabelas base.  

 ![Plan_Output_List_with_Materialized_Views](media/performance-tuning-materialized-views/output-list.png)

Com exibições materializadas, a mesma consulta é executada muito mais rapidamente sem qualquer alteração de código.  

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [Descrição geral do desenvolvimento no SQL Data Warehouse](sql-data-warehouse-overview-develop.md).
