---
title: Migrar os seus dados para uma conta da API da Cassandra no Azure Cosmos DB- Tutorial
description: Neste tutorial, aprenda a usar o comando CQL Copy & Spark para copiar dados da Apache Cassandra para uma conta Cassandra API em Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: bd2d27addb6860e49ac12eb36d8b625b8bf92001
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100410"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Tutorial: Migrar os seus dados para a conta da API cassandra no Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Como desenvolvedor, pode ter cargas de trabalho de Cassandra existentes que estão a funcionar no local ou na nuvem, e talvez queira migrar para Azure. Você pode migrar tais cargas de trabalho para uma conta Cassandra API em Azure Cosmos DB. Este tutorial fornece instruções sobre diferentes opções disponíveis para migrar dados de Apache Cassandra para a conta API cassandra em Azure Cosmos DB.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Planear a migração
> * Pré-requisitos de migração
> * Migrar dados com o comando cqlsh COPY
> * Migrar dados com o Spark

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites-for-migration"></a>Pré-requisitos de migração

* **Estimeça as suas necessidades de produção:** Antes de migrar os dados para a conta da API cassandra em Azure Cosmos DB, deve estimar as necessidades de produção da sua carga de trabalho. Em geral, recomenda-se começar com o débito médio necessário para as operações CRUD e, em seguida, incluir o débito adicional necessário para operações ETL (Extrair Transformar Carregar) ou grandes. Precisa dos seguintes detalhes para planear a migração: 

  * **Tamanho dos dados existentes ou o tamanho estimado dos dados:** define o requisito de tamanho e débito de base de dados mínimos. Se estiver a calcular o tamanho dos dados para uma nova aplicação, pode pressupor que os dados são distribuídos uniformemente nos registos e calcular o valor ao multiplicar o tamanho de dados. 

  * **Produção necessária:** Leitura aproximada (consulta/obter) e escrever (atualizar/eliminar/inserir) taxa de produção. Este valor é necessário para calcular as unidades de pedido necessárias, juntamente com o tamanho de dados no estado estável.  

  * **O esquema:** Ligue-se ao seu cluster Cassandra existente através do cqlsh e exporte o esquema de Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Depois de identificar os requisitos da sua carga de trabalho existente, deverá criar uma conta, base de dados e contentores Azure Cosmos de acordo com os requisitos de produção recolhidos.  

  * **Determine a carga RU para uma operação:** Pode determinar as RUs utilizando qualquer um dos SDKs apoiados pela API cassandra. Este exemplo mostra a versão do .NET dos encargos de obter RU.

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

* **Criar tabelas na conta API de Cassandra:** Antes de começar a migrar dados, crie todas as suas tabelas a partir do portal Azure ou a partir do cqlsh. Se estiver a migrar para uma conta Azure Cosmos que tenha produção de nível de base de dados, certifique-se de fornecer uma chave de partição ao criar os recipientes Azure Cosmos.

* **Aumentar o débito:** a duração da migração de dados depende da quantidade de débito que aprovisionou para as tabelas no Azure Cosmos DB. Aumente o débito durante o período de migração. Com o débito mais elevado, pode evitar limitações de velocidade e realizar a migração em menos tempo. Após concluir a migração, reduza o débito para reduzir os custos. Também é recomendado ter a conta Azure Cosmos na mesma região que a sua base de dados de origem. 

* **Ativar TLS:** A Azure Cosmos DB tem rigorosos requisitos e padrões de segurança. Certifique-se de ativar o TLS quando interagir com a sua conta. Quando utiliza o CQL com SSH, tem a opção de fornecer informações sobre TLS.

## <a name="options-to-migrate-data"></a>Opções de migração de dados

Pode mover dados de cargas de trabalho existentes do Cassandra para o Azure Cosmos DB com as seguintes opções:

* [Com o comando cqlsh COPY](#migrate-data-using-cqlsh-copy-command)  
* [Com o Spark](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrar dados com o comando cqlsh COPY

O [comando CQL COPY](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) é utilizado para copiar dados locais para a conta Cassandra API em Azure Cosmos DB. Utilize os seguintes passos para copiar dados:

1. Obtenha as informações da cadeia de ligação da conta da API para Cassandra:

   * Inscreva-se no [portal Azure](https://portal.azure.com)e navegue para a sua conta Azure Cosmos.

   * Abra o painel **Cadeia de Ligação** que contém todas as informações necessárias para ligar à sua conta da API para Cassandra a partir do cqlsh.

2. Inicie a sessão no cqlsh com as informações de ligação do portal.

3. Utilize o comando CQL COPY para copiar dados locais para a conta da API para Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrar dados com o Spark 

Utilize os seguintes passos para migrar dados para a conta da API de Cassandra com a Spark:

- Provisão de um [cluster Azure Databricks](cassandra-spark-databricks.md) ou um [cluster HDInsight](cassandra-spark-hdinsight.md) 

- Mover dados para o destino Cassandra API ponto final usando a [operação de cópia](cassandra-spark-table-copy-ops.md) de tabela 

A migração de dados utilizando trabalhos spark é uma opção recomendada se tiver dados que residam num cluster existente em máquinas virtuais Azure ou em qualquer outra nuvem. Esta opção requer que a Spark seja configurada como intermediário por uma vez ou ingestão regular. Você pode acelerar esta migração usando a conectividade Azure ExpressRoute entre as instalações e Azure. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não são necessários, pode apagar o grupo de recursos, a conta Azure Cosmos e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **Delete** e, em seguida, confirme o nome do grupo de recursos para eliminar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a migrar os seus dados para a conta da API cassandra em Azure Cosmos DB. Pode agora proceder ao seguinte artigo para conhecer outros conceitos DB da Azure Cosmos:

> [!div class="nextstepaction"]
> [Tunable data consistency levels in Azure Cosmos DB](../cosmos-db/consistency-levels.md) (Níveis de consistência de dados ajustáveis no Azure Cosmos DB)


