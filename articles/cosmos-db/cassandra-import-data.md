---
title: Migrar os seus dados para uma conta da API da Cassandra no Azure Cosmos DB- Tutorial
description: Neste tutorial, aprenda a usar o comando CQL Copy & Spark para copiar dados de Apache Cassandra para uma conta Cassandra API em Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
Customer intent: As a developer, I want to migrate my existing Cassandra workloads to Azure Cosmos DB so that the overhead to manage resources, clusters, and garbage collection is automatically handled by Azure Cosmos DB.
ms.openlocfilehash: c754740369da6d0a8084b9b60ef178fb28e32f1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75445678"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Tutorial: Migrar os seus dados para a conta Da API de Cassandra em Azure Cosmos DB

Como desenvolvedor, pode ter cargas de trabalho cassandra existentes que estão funcionando no local ou na nuvem, e você pode querer emigrar para Azure. Você pode migrar tais cargas de trabalho para uma conta Cassandra API em Azure Cosmos DB. Este tutorial fornece instruções sobre diferentes opções disponíveis para migrar dados da Apache Cassandra para a conta Cassandra API em Azure Cosmos DB.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Planear a migração
> * Pré-requisitos de migração
> * Migrar dados com o comando cqlsh COPY
> * Migrar dados com o Spark

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites-for-migration"></a>Pré-requisitos de migração

* **Calcule as suas necessidades de entrada:** Antes de migrar dados para a conta Cassandra API em Azure Cosmos DB, deve estimar as necessidades de entrada da sua carga de trabalho. Em geral, recomenda-se começar com o débito médio necessário para as operações CRUD e, em seguida, incluir o débito adicional necessário para operações ETL (Extrair Transformar Carregar) ou grandes. Precisa dos seguintes detalhes para planear a migração: 

  * **Tamanho dos dados existentes ou o tamanho estimado dos dados:** define o requisito de tamanho e débito de base de dados mínimos. Se estiver a calcular o tamanho dos dados para uma nova aplicação, pode pressupor que os dados são distribuídos uniformemente nos registos e calcular o valor ao multiplicar o tamanho de dados. 

  * **Entrada necessária:** Leia aproximadamente (consulta/obter) e escreva (atualizar/excluir/inserir) taxa de entrada. Este valor é necessário para calcular as unidades de pedido necessárias, juntamente com o tamanho de dados no estado estável.  

  * **O esquema:** Ligue-se ao seu aglomerado Cassandra existente através do cqlsh e exporte o esquema de Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Depois de identificar os requisitos da sua carga de trabalho existente, deverá criar uma conta, base de dados e contentores Azure Cosmos, de acordo com os requisitos de entrada recolhidos.  

  * **Determine a carga da RU para uma operação:** Você pode determinar as RUs usando qualquer um dos SDKs suportados pela API Cassandra. Este exemplo mostra a versão do .NET dos encargos de obter RU.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Alocar o débito necessário:** o Azure Cosmos DB pode dimensionar automaticamente o armazenamento e débito à medida que os requisitos aumentam. Pode calcular as necessidades de débito com a [Calculadora de unidades de pedido do Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

* **Crie tabelas na conta DaPi Cassandra:** Antes de começar a migrar dados, crie todas as suas tabelas a partir do portal Azure ou do cqlsh. Se estiver a migrar para uma conta Azure Cosmos que tem um nível de base de dados, certifique-se de fornecer uma chave de partição ao criar os recipientes Azure Cosmos.

* **Aumentar o débito:** a duração da migração de dados depende da quantidade de débito que aprovisionou para as tabelas no Azure Cosmos DB. Aumente o débito durante o período de migração. Com o débito mais elevado, pode evitar limitações de velocidade e realizar a migração em menos tempo. Após concluir a migração, reduza o débito para reduzir os custos. Também é recomendado ter a conta Azure Cosmos na mesma região que a sua base de dados de origem. 

* **Ativar o SSL:** A Azure Cosmos DB tem rigorosos requisitos e padrões de segurança. Lembre-se de ativar o SSL quando interagir com a sua conta. Quando utilizar o CQL com SSH, tem a opção de fornecer informações SSL.

## <a name="options-to-migrate-data"></a>Opções de migração de dados

Pode mover dados de cargas de trabalho existentes do Cassandra para o Azure Cosmos DB com as seguintes opções:

* [Com o comando cqlsh COPY](#migrate-data-using-cqlsh-copy-command)  
* [Com o Spark](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrar dados com o comando cqlsh COPY

O [comando CQL COPY](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) é usado para copiar dados locais para a conta Cassandra API em Azure Cosmos DB. Utilize os seguintes passos para copiar dados:

1. Obtenha as informações da cadeia de ligação da conta da API para Cassandra:

   * Inscreva-se no [portal Azure](https://portal.azure.com)e navegue na sua conta Azure Cosmos.

   * Abra o painel **Cadeia de Ligação** que contém todas as informações necessárias para ligar à sua conta da API para Cassandra a partir do cqlsh.

2. Inicie a sessão no cqlsh com as informações de ligação do portal.

3. Utilize o comando CQL COPY para copiar dados locais para a conta da API para Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrar dados com o Spark 

Utilize os seguintes passos para migrar dados para a conta DaPi Cassandra com a Spark:

- Provisionum [cluster de Databricks Azure](cassandra-spark-databricks.md) ou um [cluster HDInsight](cassandra-spark-hdinsight.md) 

- Mova os dados para o destino Cassandra API endpoint usando a operação de cópia de [tabela](cassandra-spark-table-copy-ops.md) 

Os dados migradores utilizando os trabalhos spark são uma opção recomendada se tiver dados residentes num cluster existente em máquinas virtuais Azure ou em qualquer outra nuvem. Esta opção requer que a Spark seja criada como intermediário por uma vez ou uma ingestão regular. Pode acelerar esta migração utilizando a conectividade Azure ExpressRoute entre as instalações e o Azure. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não são necessários, pode eliminar o grupo de recursos, a conta Azure Cosmos e todos os recursos relacionados. Para isso, selecione o grupo de recursos para a máquina virtual, selecione **Eliminar**, e, em seguida, confirmar o nome do grupo de recursos para apagar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a migrar os seus dados para a conta da Cassandra API em Azure Cosmos DB. Pode agora proceder ao seguinte artigo para conhecer outros conceitos de Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Tunable data consistency levels in Azure Cosmos DB](../cosmos-db/consistency-levels.md) (Níveis de consistência de dados ajustáveis no Azure Cosmos DB)


