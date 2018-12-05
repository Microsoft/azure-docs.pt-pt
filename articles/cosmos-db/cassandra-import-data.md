---
title: 'Tutorial: Migrar os dados para uma conta da API de Cassandra na Azure Cosmos DB'
description: Neste tutorial, saiba como utilizar o comando de cópia de CQL & Spark para copiar dados do Apache Cassandra para uma conta da API de Cassandra na Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 12/03/2018
ms.reviewer: sngun
Customer intent: As a developer, I want to migrate my existing Cassandra workloads to Azure Cosmos DB so that the overhead to manage resources, clusters, and garbage collection is automatically handled by Azure Cosmos DB.
ms.openlocfilehash: 604cab3bed73366ce28c8bb35b63df6379985cfb
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867472"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Tutorial: Migrar os seus dados para a conta da API de Cassandra na Azure Cosmos DB

Como desenvolvedor, pode ter cargas de trabalho Cassandra existentes que estão em execução no local ou na cloud, e poderá pretender migrá-los para o Azure. É possível migrar essas cargas de trabalho para uma conta da API de Cassandra na Azure Cosmos DB. Este tutorial fornece instruções sobre diferentes opções disponíveis para migrar dados do Apache Cassandra para a conta de API para Cassandra na Azure Cosmos DB.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Planear a migração
> * Pré-requisitos de migração
> * Migrar dados com o comando cqlsh COPY
> * Migrar dados com o Spark

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites-for-migration"></a>Pré-requisitos de migração

* **Calcular as necessidades de débito:** antes de migrar dados para a API de Cassandra de contas no Azure Cosmos DB, deve determinar as necessidades de débito da sua carga de trabalho. Em geral, recomenda-se começar com o débito médio necessário para as operações CRUD e, em seguida, incluir o débito adicional necessário para operações ETL (Extrair Transformar Carregar) ou grandes. Precisa dos seguintes detalhes para planear a migração: 

   * **Tamanho dos dados existentes ou o tamanho estimado dos dados:** define o requisito de tamanho e débito de base de dados mínimos. Se estiver a calcular o tamanho dos dados para uma nova aplicação, pode pressupor que os dados são distribuídos uniformemente nos registos e calcular o valor ao multiplicar o tamanho de dados. 

   * **Taxa de transferência necessária:** fazer uma aproximação de leitura (get/consulta) e a taxa de débito de escrita (update/delete/insert). Este valor é necessário para calcular as unidades de pedido necessárias, juntamente com o tamanho de dados no estado estável.  

   * **O esquema:** ligar ao seu cluster do Cassandra existente por meio de cqlsh e exportar o esquema do Cassandra: 

     ```bash
     cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
     ```

   Depois de identificar os requisitos da sua carga de trabalho existente, deve criar uma conta do Cosmos do Azure, base de dados e contentores, de acordo com os requisitos de débito recolhidos.  

   * **Determinar a taxa de RU uma operação:** pode determinar o RUs utilizando qualquer um dos SDKs suportados pela API do Cassandra. Este exemplo mostra a versão do .NET dos encargos de obter RU.

     ```csharp
     var tableInsertStatement = table.Insert(sampleEntity);
     var insertResult = await tableInsertStatement.ExecuteAsync();

     foreach (string key in insertResult.Info.IncomingPayload)
       {
          byte[] valueInBytes = customPayload[key];
          string value = Encoding.UTF8.GetString(valueInBytes);
          Console.WriteLine($"CustomPayload:  {key}: {value}");
       }
     ```

* **Alocar o débito necessário:** o Azure Cosmos DB pode dimensionar automaticamente o armazenamento e débito à medida que os requisitos aumentam. Pode calcular as necessidades de débito com a [Calculadora de unidades de pedido do Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

* **Criar tabelas na conta de API para Cassandra:** antes de começar a migração de dados, criar previamente todas as tabelas do portal do Azure ou a partir de cqlsh. Se estiver a migrar para uma conta do Cosmos do Azure que tenha o débito de nível de base de dados, certifique-se fornecer uma chave de partição quando criar os contentores do Azure Cosmos.

* **Aumentar o débito:** a duração da migração de dados depende da quantidade de débito que aprovisionou para as tabelas no Azure Cosmos DB. Aumente o débito durante o período de migração. Com o débito mais elevado, pode evitar limitações de velocidade e realizar a migração em menos tempo. Após concluir a migração, reduza o débito para reduzir os custos. É também recomendado para que a conta do Cosmos do Azure na mesma região que a sua base de dados de origem. 

* **Ativar o SSL:** o Azure Cosmos DB tem requisitos e normas de segurança estritos. Lembre-se de ativar o SSL quando interagir com a sua conta. Quando utilizar o CQL com SSH, tem a opção de fornecer informações SSL.

## <a name="options-to-migrate-data"></a>Opções de migração de dados

Pode mover dados de cargas de trabalho existentes do Cassandra para o Azure Cosmos DB com as seguintes opções:

* [Com o comando cqlsh COPY](#using-cqlsh-copy-command)  
* [Com o Spark](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrar dados com o comando cqlsh COPY

O [comando de cópia de CQL](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) é usado para copiar dados locais para a conta de API para Cassandra na Azure Cosmos DB. Utilize os seguintes passos para copiar dados:

1. Obtenha as informações da cadeia de ligação da conta da API para Cassandra:

   * Inicie sessão para o [portal do Azure](https://portal.azure.com)e navegue para a sua conta do Cosmos do Azure.

   * Abra o painel **Cadeia de Ligação** que contém todas as informações necessárias para ligar à sua conta da API para Cassandra a partir do cqlsh.

2. Inicie a sessão no cqlsh com as informações de ligação do portal.

3. Utilize o comando CQL COPY para copiar dados locais para a conta da API para Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrar dados com o Spark 

Utilize os seguintes passos para migrar dados para a conta de API para Cassandra com o Spark:

- Aprovisionar um [cluster do Azure Databricks](cassandra-spark-databricks.md) ou um [cluster do HDInsight](cassandra-spark-hdinsight.md) 

- Mover dados para o ponto final de Cassandra API de destino com o [operação de cópia de tabela](cassandra-spark-table-copy-ops.md) 

Migração de dados com tarefas do Spark são uma opção recomendada se tiver dados que residem num cluster existente em máquinas virtuais do Azure ou qualquer outra cloud. Esta opção requer o Spark ser definida como um intermediário para um tempo ou uma ingestão de regular. Pode acelerar a migração, utilizando a conectividade do ExpressRoute do Azure entre locais e o Azure. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a conta do Cosmos do Azure e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **eliminar**e, em seguida, confirme o nome do grupo de recursos para eliminar.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a migrar os dados para a conta da API de Cassandra na Azure Cosmos DB. Agora, pode avançar para o seguinte artigo para saber mais sobre outros conceitos do Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Tunable data consistency levels in Azure Cosmos DB](../cosmos-db/consistency-levels.md) (Níveis de consistência de dados ajustáveis no Azure Cosmos DB)


