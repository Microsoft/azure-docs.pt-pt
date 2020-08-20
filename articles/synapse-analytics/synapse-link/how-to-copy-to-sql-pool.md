---
title: Copiar Link Synapse para dados DB do Azure Cosmos numa piscina SQL usando Apache Spark
description: Carregue os dados num dataframe da Spark, cuide dos dados e carregue-os numa mesa de bilhar SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 88962d63519cfeb78be694c4f702b05ed4e7d3df
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658517"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-sql-pool-using-apache-spark"></a>Copiar dados da Azure Cosmos DB em uma piscina SQL usando Apache Spark

A azure Synapse Link for Azure Cosmos DB permite que os utilizadores corram perto de análises em tempo real sobre dados operacionais em Azure Cosmos DB. No entanto, há momentos em que alguns dados precisam de ser agregados e enriquecidos para servir os utilizadores de armazéns de dados. A curadoria e exportação de dados do Synapse Link pode ser feita com apenas algumas células num caderno.

## <a name="prerequisites"></a>Pré-requisitos
* [Disposição de um espaço de trabalho synapse](../quickstart-create-workspace.md) com:
    * [Piscina de faíscas](../quickstart-create-apache-spark-pool-studio.md)
    * [Conjunto de SQL](../quickstart-create-sql-pool-studio.md)
* [Provisione uma conta Cosmos DB com um recipiente HTAP com dados](../../cosmos-db/configure-synapse-link.md)
* [Ligue o recipiente Azure Cosmos DB HTAP ao espaço de trabalho](./how-to-connect-synapse-link-cosmos-db.md)
* [Tenha a configuração certa para importar dados em uma piscina SQL da Spark](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Passos
Neste tutorial, irá ligar-se à loja analítica para que não haja impacto na loja transacional (não consumirá nenhuma Unidade de Pedido). Iremos seguir os seguintes passos:
1. Leia o recipiente Cosmos DB HTAP num dataframe spark
2. Agregar os resultados num novo dataframe
3. Ingerir os dados numa piscina SQL

[![Faísca para passos SQL](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Dados
Neste exemplo, utilizamos um contentor HTAP chamado **RetailSales.** Faz parte de um serviço ligado chamado **ConnectedData,** e tem o seguinte esquema:
* _rid: corda (nuvel = verdade)
* _ts: longo (anulado = verdadeiro)
* logQuantity: duplo (anulado = verdadeiro)
* produto Código: cadeia (anulado = verdadeiro)
* quantidade: longa (anulada = verdadeira)
* preço: longo (anulado = verdadeiro)
* id: corda (anulado = verdadeiro)
* publicidade: longo (anulado = verdadeiro)
* storeId: longo (anulado = verdadeiro)
* weekStarting: longo (anulado = verdadeiro)
* _etag: corda (anulado = verdadeiro)

Agregaremos as vendas *(quantidade,* *receita* (preço x quantidade) por *produtoCode* e *weekStarting* para efeitos de reporte. Finalmente, exportaremos esses dados para uma mesa de bilhar SQL chamada **dbo.productsales.**

## <a name="configure-a-spark-notebook"></a>Configure um caderno de faíscas
Crie um caderno Spark com Scala como Spark (Scala) como a língua principal. Utilizamos a definição padrão do caderno para a sessão.

## <a name="read-the-data-in-spark"></a>Leia os dados em Spark
Leia o recipiente Cosmos DB HTAP com a Spark num dataframe na primeira célula.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>Agregar os resultados num novo dataframe

Na segunda célula, fazemos a transformação e os agregados necessários para o novo dataframe antes de o carregar numa base de dados de piscinas SQL.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-sql-pool"></a>Carregue os resultados numa piscina SQL

Na terceira célula, colocamos os dados numa piscina SQL. Criará automaticamente uma tabela externa temporária, fonte de dados externa e formato de ficheiros externos que será eliminado assim que o trabalho for feito.

```java
df_olap_aggr.write.sqlanalytics("arnaudpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Consultar os resultados com o SQL

Pode consultar o resultado usando uma consulta SQL simples, como o seguinte script SQL:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

A sua consulta apresentará os seguintes resultados num modo gráfico: [ ![ Spark to SQL Steps](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>Passos seguintes
* [Consulta Azure Cosmos DB Loja Analítica com Faísca Apache](./how-to-query-analytical-store-spark.md)