---
title: Vincular um Azure Cosmos DB à aplicação Azure Spring Cloud
description: Saiba como ligar a Azure Cosmos DB à sua aplicação Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 20926988d554d562f36587734bd99c34b4731e69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92088725"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Ligue uma base de dados DB do Azure Cosmos à sua aplicação Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Em vez de configurar manualmente as suas aplicações Boot Spring Boot, pode ligar automaticamente os serviços Azure às suas aplicações utilizando a Azure Spring Cloud. Este artigo demonstra como ligar a sua aplicação a uma base de dados DB da Azure Cosmos.

Pré-requisitos:

* Um exemplo de Azure Spring Cloud. Siga o nosso [quickstart na implantação através do CLI Azure](spring-cloud-quickstart.md) para começar.
* Uma conta DB da Azure Cosmos com um nível mínimo de autorização de Contribuinte.

## <a name="bind-azure-cosmos-db"></a>Bind Azure Cosmos DB

A Azure Cosmos DB tem cinco tipos diferentes de API que suportam a ligação. O seguinte procedimento mostra como utilizá-los:

1. Crie uma base de dados do Azure Cosmos DB. Consulte o quickstart sobre a [criação de uma base de dados](../cosmos-db/create-cosmosdb-resources-portal.md) para obter ajuda. 

1. Grave o nome da sua base de dados. Para este procedimento, o nome da base de dados é **testdb**.

1. Adicione uma das seguintes dependências ao ficheiro de pom.xml da sua aplicação Azure Spring Cloud. Escolha a dependência adequada para o seu tipo API.

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

1. Utilize `az spring-cloud app update` para atualizar a implementação atual ou use para criar uma nova `az spring-cloud app deployment create` implementação. Estes comandos irão atualizar ou criar a aplicação com a nova dependência.

1. Aceda à sua página de serviço Azure Spring Cloud no portal Azure. Vá ao **Painel de Aplicações** e selecione a aplicação para ligar ao Azure Cosmos DB. Esta aplicação é a mesma que atualizou ou implementou no passo anterior.

1. Selecione **a ligação de serviços** e selecione **Criar ligação de serviço**. Para preencher o formulário, selecione:
   * O valor **do tipo de ligação** **Azure Cosmos DB**.
   * O tipo API.
   * O nome da sua base de dados.
   * A conta DB de Azure Cosmos.

    > [!NOTE]
    > Se estiver a usar a Cassandra, use um espaço chave para o nome da base de dados.

1. Reinicie a aplicação selecionando **Reiniciar** na página de aplicação.

1. Para garantir que o serviço está corretamente vinculado, selecione o nome de encadernação e verifique os seus detalhes. O `property` campo deve ser semelhante a este exemplo:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ligar a sua aplicação Azure Spring Cloud a uma base de dados DB do Azure Cosmos. Para saber mais sobre serviços de encadernação à sua aplicação, consulte [Bind to a Azure Cache for Redis cache](spring-cloud-tutorial-bind-redis.md).