---
title: Migrar os seus dados para uma conta da API da Cassandra no Azure Cosmos DB- Tutorial
description: Neste tutorial, aprenda a copiar dados de Apache Cassandra para uma conta Cassandra API em Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7c55cbf4b8739a885c499c820a7e68825522ea4e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449295"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Tutorial: Migrar os seus dados para uma conta API cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Como desenvolvedor, pode ter cargas de trabalho de Cassandra existentes que estão a funcionar no local ou na nuvem, e talvez queira migrar para Azure. Você pode migrar tais cargas de trabalho para uma conta Cassandra API em Azure Cosmos DB. Este tutorial fornece instruções sobre diferentes opções disponíveis para migrar dados de Apache Cassandra para a conta API cassandra em Azure Cosmos DB.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Planear a migração
> * Pré-requisitos de migração
> * Migrar dados usando o `cqlsh` `COPY` comando
> * Migrar dados utilizando o Spark

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites-for-migration"></a>Pré-requisitos de migração

* **Estimeça as suas necessidades de produção:** Antes de migrar os dados para a conta da API cassandra em Azure Cosmos DB, deve estimar as necessidades de produção da sua carga de trabalho. Em geral, comece com a produção média exigida pelas operações CRUD e, em seguida, inclua a produção adicional necessária para a carga de transformação de extrato ou operações espinhosas. Precisa dos seguintes detalhes para planear a migração: 

  * **Tamanho dos dados existentes ou o tamanho estimado dos dados:** define o requisito de tamanho e débito de base de dados mínimos. Se estimar o tamanho dos dados para uma nova aplicação, pode assumir que os dados são distribuídos uniformemente pelas linhas e estimar o valor multiplicando-se com o tamanho dos dados. 

  * **Produção necessária:** Taxa de produção aproximada das operações de leitura (consulta/obter) e escrita (atualizar/eliminar/inserir). Este valor é necessário para calcular as unidades de pedido necessárias, juntamente com o tamanho dos dados do estado estaciono.  

  * **O esquema:** Ligue-se ao seu cluster Cassandra existente `cqlsh` através de, e exporte o esquema de Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Depois de identificar os requisitos da sua carga de trabalho existente, crie uma conta DB, base de dados e contentores Azure Cosmos, de acordo com os requisitos de produção recolhidos.  

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

* **Criar tabelas na conta API de Cassandra:** Antes de começar a migrar dados, crie todas as suas tabelas a partir do portal Azure ou a partir de `cqlsh` . Se estiver a migrar para uma conta DB Azure Cosmos que tenha uma produção de nível de base, certifique-se de fornecer uma chave de partição quando criar os recipientes.

* **Aumentar o débito:** a duração da migração de dados depende da quantidade de débito que aprovisionou para as tabelas no Azure Cosmos DB. Aumente o débito durante o período de migração. Com o débito mais elevado, pode evitar limitações de velocidade e realizar a migração em menos tempo. Após concluir a migração, reduza o débito para reduzir os custos. Recomendamos também que tenha a conta DB da Azure Cosmos na mesma região que a sua base de dados de origem. 

* **Ativar TLS:** A Azure Cosmos DB tem rigorosos requisitos e padrões de segurança. Certifique-se de ativar o TLS quando interagir com a sua conta. Quando utiliza o CQL com SSH, tem a opção de fornecer informações sobre TLS.

## <a name="options-to-migrate-data"></a>Opções de migração de dados

Pode mover dados das cargas de trabalho de Cassandra existentes para a Azure Cosmos DB utilizando o `cqlsh` `COPY` comando, ou usando o Spark. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Migrar dados utilizando o comando cqlsh COPY

Utilize o [comando CQL COPY](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) para copiar dados locais para a conta API cassandra em Azure Cosmos DB.

1. Obtenha as informações da cadeia de ligação da conta da API para Cassandra:

   * Inscreva-se no [portal Azure](https://portal.azure.com)e vá à sua conta DB Azure Cosmos.

   * Abra o painel **de cordas de ligação.** Aqui vê toda a informação que precisa para ligar à sua conta Cassandra API a partir de `cqlsh` .

1. Inscreva-se `cqlsh` utilizando as informações de ligação do portal.

1. Utilize o `CQL` `COPY` comando para copiar dados locais para a conta API de Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Migrar dados utilizando o Spark 

Utilize os seguintes passos para migrar dados para a conta da API de Cassandra com a Spark:

1. Provisão de um [cluster Azure Databricks](cassandra-spark-databricks.md) ou um [cluster Azure HDInsight](cassandra-spark-hdinsight.md). 

1. Mover dados para o destino Cassandra API ponto final utilizando a [operação de cópia](cassandra-spark-table-copy-ops.md)de tabela . 

A migração de dados utilizando trabalhos spark é uma opção recomendada se tiver dados que residam num cluster existente em máquinas virtuais Azure ou em qualquer outra nuvem. Para isso, deve configurar a Spark como um intermediário para uma ingestão única ou regular. Pode acelerar esta migração utilizando a conectividade Azure ExpressRoute entre o seu ambiente no local e o Azure. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não são necessários, pode apagar o grupo de recursos, a conta DB do Azure Cosmos e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **Delete** e, em seguida, confirme o nome do grupo de recursos para eliminar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a migrar os seus dados para uma conta da API da Cassandra em Azure Cosmos DB. Agora pode aprender sobre outros conceitos em Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Tunable data consistency levels in Azure Cosmos DB](../cosmos-db/consistency-levels.md) (Níveis de consistência de dados ajustáveis no Azure Cosmos DB)




