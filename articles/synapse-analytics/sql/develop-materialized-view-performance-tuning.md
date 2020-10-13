---
title: Otimização do desempenho com vistas materializadas
description: Recomendações e considerações para visualizações materializadas para melhorar o seu desempenho de consulta.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 1f04f8b447f07f62561f56722df3b9502ad58d41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289043"
---
# <a name="performance-tuning-with-materialized-views"></a>Otimização do desempenho com vistas materializadas

Na piscina Synapse SQL, as vistas materializadas proporcionam um método de manutenção baixo para consultas analíticas complexas para obter um desempenho rápido sem qualquer alteração de consulta. Este artigo discute as orientações gerais sobre a utilização de pontos de vista materializados.

## <a name="materialized-views-vs-standard-views"></a>Vistas materializadas vs. vistas padrão

A piscina SQL suporta vistas standard e materializada.  Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas a consultas como tabelas lógicas.  As vistas revelam a complexidade da computação comum de dados e adicionam uma camada de abstração às alterações de cálculo para que não haja necessidade de reescrever consultas.  

Uma visão padrão calcula os seus dados cada vez que a vista é usada.  Não há dados armazenados no disco. As pessoas normalmente usam vistas padrão como uma ferramenta que ajuda a organizar os objetos lógicos e consultas numa base de dados.  Para utilizar uma visão padrão, uma consulta precisa de fazer referência direta a ela.

Uma vista materializada pré-computa, armazena e mantém os seus dados em piscina SQL como uma mesa.  A recomputação não é necessária cada vez que uma vista materializada é usada.  É por isso que as consultas que usam todos ou um subconjunto dos dados em vistas materializadas podem ganhar um desempenho mais rápido.  Ainda melhor, as consultas podem usar uma visão materializada sem fazer referência direta a ela, por isso não há necessidade de alterar o código de aplicação.  

A maioria dos requisitos de visão padrão ainda se aplicam a uma visão materializada. Para obter mais informações sobre a sintaxe de vista materializada e outros requisitos, consulte a [VISÃO MATERIALIZADA COMO SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

| Comparação                     | Vista                                         | Vista materializada
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Ver definição                 | Armazenado no armazém de dados Azure.              | Armazenado no armazém de dados Azure.
|Ver conteúdos                    | Gerada cada vez que a vista é usada.   | Pré-processado e armazenado no armazém de dados Azure durante a criação de visualização. Atualizado à medida que os dados são adicionados às tabelas subjacentes.
|Atualização de dados                    | Sempre atualizado                               | Sempre atualizado
|Velocidade para recuperar dados de visualização de consultas complexas     | Lento                                         | Rápido  
|Armazenamento extra                   | Não                                           | Sim
|Syntax                          | CRIAR VISTA                                  | CRIAR VISTA MATERIALIZADA COMO SELEÇÃO

## <a name="benefits-of-materialized-views"></a>Benefícios de vistas materializadas

Uma visão materializada devidamente concebida proporciona os seguintes benefícios:

- Tempo de execução reduzido para consultas complexas com JOINs e funções agregadas. Quanto mais complexa for a consulta, maior é o potencial de poupança no tempo de execução. O maior benefício é obtido quando o custo de computação de uma consulta é elevado e o conjunto de dados resultante é pequeno.  

- O otimizador no pool SQL pode automaticamente usar vistas materializadas implantadas para melhorar os planos de execução de consultas.  Este processo é transparente para os utilizadores que fornecem um desempenho de consulta mais rápido e não requer consultas para fazer referência direta às vistas materializadas.

- Requer pouca manutenção nas vistas.  Uma visão materializada armazena dados em dois lugares, um índice de loja de colunas agrupado para os dados iniciais no tempo de criação de visualização, e uma loja delta para as mudanças incrementais de dados.  Todas as alterações de dados das tabelas base são automaticamente adicionadas à loja delta de forma sincronizada.  Um processo de fundo (tuple mover) move periodicamente os dados da loja delta para o índice de loja de colunas da vista.  Este design permite que a consulta de vistas materializadas devolva os mesmos dados que consulta diretamente as tabelas base.
- Os dados numa vista materializada podem ser distribuídos de forma diferente das tabelas base.  
- Os dados em vistas materializadas recebem os mesmos benefícios de alta disponibilidade e resiliência que os dados em tabelas regulares.  

Em comparação com outros fornecedores de armazéns de dados, as vistas materializadas implementadas no pool SQL também proporcionam os seguintes benefícios adicionais:

- Atualização automática e sincronizada de dados com alterações de dados nas tabelas base. Não é necessária qualquer ação do utilizador.
- Suporte de função agregado alargado. Consulte [CREATE VISTA MATERIALIZADA COMO SELECT (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- O apoio à recomendação de visualização materializada específica da consulta.  Ver [EXPLAIN (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="common-scenarios"></a>Cenários comuns  

As vistas materializadas são normalmente utilizadas em seguintes cenários:

**Precisa melhorar o desempenho de consultas analíticas complexas contra grandes dados em tamanho**

Consultas analíticas complexas normalmente usam mais funções de agregação e juntas de mesa, causando operações mais pesadas de computação, tais como baralhar e juntar-se na execução de consultas.  É por isso que essas consultas demoram mais tempo a ser completadas, particularmente em mesas grandes.  

Os utilizadores podem criar visualizações materializadas para os dados devolvidos a partir dos cálculos comuns das consultas, pelo que não é necessária recomputação quando estes dados são necessários por consultas, permitindo um menor custo de computação e uma resposta de consulta mais rápida.

**Precisa de um desempenho mais rápido sem alterações de consulta mínimas**

As alterações de esquema e consulta nos armazéns de dados são normalmente mantidas ao mínimo para suportar operações e relatórios regulares da ETL.  As pessoas podem usar vistas materializadas para afinação de desempenho de consulta se o custo incorrido pelas vistas pode ser compensado pelo ganho no desempenho da consulta.

Em comparação com outras opções de afinação, como a escala e a gestão de estatísticas, é uma mudança de produção muito menos impactante para criar e manter uma visão materializada e o seu potencial ganho de desempenho também é maior.

- Criar ou manter vistas materializadas não afeta as consultas que correm contra as tabelas base.
- O otimizador de consultas pode utilizar automaticamente as vistas materializadas implementadas sem referência de visualização direta numa consulta. Esta capacidade reduz a necessidade de mudança de consulta na afinação de desempenho.

**Precisa de diferentes estratégias de distribuição de dados para um desempenho de consulta mais rápido**

O armazém de dados Azure é um sistema de processamento distribuído e massivamente paralelo (MPP).   Os dados numa tabela de armazéns de dados são distribuídos por 60 nóns utilizando uma das três [estratégias](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) de distribuição (haxixe, round_robin ou replicado).  

A distribuição de dados é especificada na hora de criação da tabela e permanece inalterada até que a tabela seja largada. A vista materializada sendo uma tabela virtual no disco suporta a distribuição de dados de haxixe e round_robin.  Os utilizadores podem escolher uma distribuição de dados diferente das tabelas base, mas ideal para o desempenho de consultas que frequentemente usam as vistas.  

## <a name="design-guidance"></a>Orientação de design

Aqui está a orientação geral sobre a utilização de vistas materializadas para melhorar o desempenho da consulta:

**Design para a sua carga de trabalho**

Antes de começar a criar vistas materializadas, é importante ter uma compreensão profunda da sua carga de trabalho em termos de padrões de consulta, importância, frequência e o tamanho dos dados resultantes.  

Os utilizadores podem executar explicação WITH_RECOMMENDATIONS <SQL_statement> para as vistas materializadas recomendadas pelo otimizador de consultas.  Uma vez que estas recomendações são específicas da consulta, uma visão materializada que beneficia uma única consulta pode não ser ideal para outras consultas na mesma carga de trabalho.  

Avalie estas recomendações com as suas necessidades de carga de trabalho em mente.  As vistas materializadas ideais são aquelas que beneficiam o desempenho da carga de trabalho.  

**Esteja ciente da troca entre consultas mais rápidas e o custo**

Para cada vista materializada, há um custo de armazenamento de dados e um custo para manter a vista.  À medida que os dados mudam nas tabelas base, o tamanho da vista materializada aumenta e a sua estrutura física também muda.  

Para evitar a degradação do desempenho da consulta, cada visão materializada é mantida separadamente pelo motor do armazém de dados, incluindo filas móveis da loja delta para os segmentos do índice de loja de colunas e consolidando alterações de dados.  

A carga de trabalho de manutenção sobe mais quando o número de visualizações materializadas e as alterações na tabela base aumentam.   Os utilizadores devem verificar se o custo incorrido de todas as vistas materializadas pode ser compensado pelo ganho de desempenho da consulta.  

Pode executar esta consulta para a lista de visualizações materializadas numa base de dados:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Opções para reduzir o número de visualizações materializadas:

- Identifique conjuntos de dados comuns frequentemente utilizados pelas consultas complexas na sua carga de trabalho.  Crie vistas materializadas para armazenar esses conjuntos de dados para que o otimizador possa usá-los como blocos de construção ao criar planos de execução.  

- Largue as vistas materializadas que tenham pouca utilização ou que já não sejam necessárias.  Uma visão materializada desativada não é mantida, mas continua a incorrer em custos de armazenamento.  

- Combine vistas materializadas criadas nas mesmas tabelas base ou similares, mesmo que os seus dados não se sobreponham.  A combinação de vistas materializadas poderia resultar numa visão maior em tamanho do que a soma das vistas separadas, no entanto, o custo de manutenção da vista deve reduzir.  Por exemplo:

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

**Nem todas as afinações de desempenho requerem mudança de consulta**

O otimizador de armazém de dados pode automaticamente usar vistas materializadas implantadas para melhorar o desempenho da consulta.  Este suporte é aplicado de forma transparente a consultas que não referenciam as vistas e a consultas que usam agregados não suportados na criação de vistas materializadas.  Não é necessária qualquer alteração de consulta. Pode verificar o plano de execução estimado de uma consulta para confirmar se é utilizada uma vista materializada.  

**Monitorar vistas materializadas**

Uma vista materializada é armazenada no armazém de dados tal como uma tabela com índice de loja de colunas agrupado (CCI).  Ler dados de uma vista materializada inclui digitalizar o índice e aplicar alterações a partir da loja delta.  Quando o número de linhas na loja delta é demasiado elevado, a resolução de uma consulta a partir de uma vista materializada pode demorar mais do que consultar diretamente as tabelas base.  

Para evitar a degradação do desempenho da consulta, é uma boa prática executar [a DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) monitorizar o overhead_ratio da vista (total_rows/base_view_row).  Se o overhead_ratio for demasiado elevado, considere reconstruir a vista materializada para que todas as linhas da loja delta sejam transferidas para o índice da loja de colunas.  

**Vista materializada e conjunto de resultados**

Estas duas funcionalidades são introduzidas na piscina SQL por volta do mesmo tempo para afinação de desempenho de consulta. O cache do conjunto de resultados é usado para alcançar tempos de alta concordância e resposta rápida de consultas repetitivas contra dados estáticos.  

Para utilizar o resultado em cache, a forma da consulta de pedido de cache deve coincidir com a consulta que produziu a cache.  Além disso, o resultado em cache deve aplicar-se a toda a consulta.  

As vistas materializadas permitem alterações de dados nas tabelas base.  Os dados em vistas materializadas podem ser aplicados a uma peça de consulta.  Este suporte permite que as mesmas vistas materializadas sejam usadas por diferentes consultas que partilhem algum cálculo para um desempenho mais rápido.

## <a name="example"></a>Exemplo

Este exemplo usa uma consulta semelhante ao TPCDS que encontra clientes que gastam mais dinheiro através do catálogo do que nas lojas. Identifica igualmente os clientes preferenciais e o seu país/região de origem.   A consulta envolve a seleção de registos TOP 100 da UNIÃO de três declarações sub-SELECION Envolvendo SUM() e GROUP BY.

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

Verifique o plano de execução estimado da consulta.  Há 18 baralhados e 17 juntas de operações, que demoram mais tempo a ser executadas. Agora vamos criar uma vista materializada para cada uma das três declarações sub-SELECÇÃO.

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

Verifique novamente o plano de execução da consulta original.  Agora o número de juntas muda de 17 para 5 e não há mais confusão.  Selecione o ícone de funcionamento do filtro no plano. A sua Lista de Saídas mostra que os dados são lidos a partir das vistas materializadas em vez de tabelas base.  

 ![Plan_Output_List_with_Materialized_Views](./media/develop-materialized-view-performance-tuning/output-list.png)

Com vistas materializadas, a mesma consulta corre muito mais rápido sem qualquer alteração de código.  

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a visão geral do [desenvolvimento do Synapse SQL.](develop-overview.md)
 