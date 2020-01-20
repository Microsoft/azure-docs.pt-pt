---
title: Tutorial-associar um Azure Cosmos DB ao seu aplicativo Azure Spring Cloud
description: Neste tutorial, saiba como associar Azure Cosmos DB ao seu aplicativo Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 1566b6ab59e858217adcf6818e1d62f851f37eb1
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277569"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Associar um banco de dados Azure Cosmos DB ao seu aplicativo Azure Spring Cloud

Em vez de configurar manualmente seus aplicativos Spring boot, você pode associar automaticamente os serviços do Azure selecionados aos seus aplicativos usando o Azure Spring Cloud. Este artigo demonstra como associar seu aplicativo a um banco de dados Azure Cosmos DB.

Pré-requisitos:

* Uma instância do Azure Spring Cloud implantada. Siga nosso [início rápido na implantação por meio do CLI do Azure](spring-cloud-quickstart-launch-app-cli.md) para começar.
* Uma conta de Azure Cosmos DB com um nível de permissão mínimo de colaborador.

## <a name="bind-azure-cosmos-db"></a>Associar Azure Cosmos DB

Azure Cosmos DB tem cinco tipos de API diferentes que dão suporte à associação. O procedimento a seguir mostra como usá-los:

1. Crie uma base de dados do Azure Cosmos DB. Consulte o guia de início rápido sobre como [criar um banco de dados](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) para obter ajuda. 

1. Registre o nome do seu banco de dados. Para esse procedimento, o nome do banco de dados é **TestDB**.

1. Adicione uma das seguintes dependências ao arquivo pom. XML do aplicativo de nuvem Spring do Azure. Escolha a dependência apropriada para o tipo de API.

    * Tipo de API: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * Tipo de API: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Tipo de API: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Tipo de API: Gremlin (grafo)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * Tipo de API: tabela do Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Use `az spring-cloud app update` para atualizar a implantação atual ou use `az spring-cloud app deployment create` para criar uma nova implantação. Esses comandos atualizarão ou criarão o aplicativo com a nova dependência.

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure. Vá para o **painel do aplicativo** e selecione o aplicativo a ser associado a Azure Cosmos DB. Esse aplicativo é o mesmo que você atualizou ou implantou na etapa anterior.

1. Selecione **Associação de serviço**e selecione **criar Associação de serviço**. Para preencher o formulário, selecione:
   * O valor do **tipo de associação** **Azure Cosmos DB**.
   * O tipo de API.
   * O nome do banco de dados.
   * A conta de Azure Cosmos DB.

    > [!NOTE]
    > Se você estiver usando o Cassandra, use um espaço de chave para o nome do banco de dados.

1. Reinicie o aplicativo selecionando **reiniciar** na página do aplicativo.

1. Para garantir que o serviço esteja associado corretamente, selecione o nome da associação e verifique seus detalhes. O campo `property` deve ser semelhante a este exemplo:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a associar seu aplicativo de nuvem Spring do Azure a um banco de dados Azure Cosmos DB. Para saber como associar seu aplicativo a um cache do Azure para cache Redis, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como associar a um cache do Azure para cache Redis](spring-cloud-tutorial-bind-redis.md)
