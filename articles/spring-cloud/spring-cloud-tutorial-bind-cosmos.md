---
title: Tutorial - Ligue um Azure Cosmos DB à sua aplicação Azure Spring Cloud
description: Neste tutorial, aprenda a ligar o Azure Cosmos DB à sua aplicação Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 1566b6ab59e858217adcf6818e1d62f851f37eb1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277569"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Ligue uma base de dados Azure Cosmos DB à sua aplicação Azure Spring Cloud

Em vez de configurar manualmente as suas aplicações spring boot, pode ligar automaticamente os serviços Azure às suas aplicações utilizando a Nuvem de primavera Azure. Este artigo demonstra como ligar a sua aplicação a uma base de dados Azure Cosmos DB.

Pré-requisitos:

* Um caso de Nuvem de primavera Azure implantado. Acompanhe o nosso [arranque rápido na implantação através do Azure CLI](spring-cloud-quickstart-launch-app-cli.md) para começar.
* Uma conta Azure Cosmos DB com um nível mínimo de permissão de Contribuinte.

## <a name="bind-azure-cosmos-db"></a>Bind Azure Cosmos DB

A Azure Cosmos DB tem cinco tipos diferentes de API que suportam a ligação. O seguinte procedimento mostra como usá-los:

1. Crie uma base de dados do Azure Cosmos DB. Consulte o quickstart na [criação](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) de uma base de dados para obter ajuda. 

1. Grave o nome da sua base de dados. Para este procedimento, o nome da base de dados é **testdb**.

1. Adicione uma das seguintes dependências ao ficheiro pom.xml da aplicação Azure Spring Cloud. Escolha a dependência adequada para o seu tipo DePi.

    * Tipo API: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * Tipo API: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Tipo API: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Tipo API: Gremlin (gráfico)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * Tipo API: Tabela Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Utilize `az spring-cloud app update` para atualizar a implementação atual ou utilizar `az spring-cloud app deployment create` para criar uma nova implementação. Estes comandos irão atualizar ou criar a aplicação com a nova dependência.

1. Vá à sua página de serviço Azure Spring Cloud no portal Azure. Vá ao **Application Dashboard** e selecione a aplicação para se ligar ao Azure Cosmos DB. Esta aplicação é a mesma que atualizou ou implementou no passo anterior.

1. Selecione **a ligação do serviço,** e selecione **Criar a ligação do serviço**. Para preencher o formulário, selecione:
   * O **valor do tipo de ligação** **Azure Cosmos DB**.
   * Do tipo API.
   * O nome da sua base de dados.
   * A conta Azure Cosmos DB.

    > [!NOTE]
    > Se estiver a usar a Cassandra, utilize um espaço chave para o nome da base de dados.

1. Reiniciar a aplicação selecionando **Reiniciar** na página de aplicação.

1. Para garantir que o serviço está corretamente ligado, selecione o nome de ligação e verifique os seus dados. O `property` campo deve ser semelhante a este exemplo:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar a sua aplicação Azure Spring Cloud a uma base de dados Azure Cosmos DB. Para aprender a ligar a sua aplicação a um Cache Azure para redis cache, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Aprenda a ligar-se a um Cache Azure para cache Redis](spring-cloud-tutorial-bind-redis.md)
