---
title: Quickstart - Use Spring Data Azure Cosmos DB v3 para criar uma base de dados de documentos usando Azure Cosmos DB
description: Este quickstart apresenta uma amostra de código DB V3 do Spring Data Azure Cosmos que pode utilizar para ligar e consultar o API API API AZURE Cosmos DB SQL
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: f31eb0fa6dbb881f7a09b21b9dd4842fdfd291f5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090295"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Quickstart: Construa uma app DB D3 da Spring Data Cosmos para gerir dados API API AZURE Cosmos DB SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [SDK v4 de Java](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Neste quickstart, você cria e gere uma conta API API AZURE Cosmos DB SQL a partir do portal Azure, e usando uma aplicação DB DB DB de Dados de primavera clonada a partir do GitHub. Primeiro, cria uma conta Azure Cosmos DB SQL API utilizando o portal Azure, depois cria uma aplicação Boot de primavera utilizando o conector DB DB V3 do Spring Data Azure Cosmos e, em seguida, adiciona recursos à sua conta Cosmos DB utilizando a aplicação Spring Boot. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, valor-chave e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

> [!IMPORTANT]  
> Estas notas de lançamento são para a versão 3 da Spring Data Azure Cosmos DB. Pode encontrar [aqui as notas de lançamento para a versão 2](sql-api-sdk-java-spring-v2.md). 
>
> Dados de primavera Azure Cosmos DB suporta apenas o SQL API.
>
> Consulte estes artigos para obter informações sobre dados da primavera sobre outras APIs Azure Cosmos DB:
> * [Dados da primavera para Apache Cassandra com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Dados de primavera MongoDB com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente a Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure. Também pode utilizar o [Emulador Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Kit de Desenvolvimento de Java (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a `JAVA_HOME` variável ambiente para a pasta onde o JDK está instalado.
- Um [arquivo binário de Maven.](https://maven.apache.org/download.cgi) Em Ubuntu, corra `apt-get install maven` para instalar Maven.
- [Git.](https://www.git-scm.com/downloads) Em Ubuntu, corra `sudo apt-get install git` para instalar Git.

## <a name="introductory-notes"></a>Notas introdutórias

*A estrutura de uma conta de Cosmos DB.* Independentemente da API ou da linguagem de programação, uma *conta* Cosmos DB contém zero ou mais *bases de dados,* uma base *de dados* (DB) contém zero ou mais *contentores* , e um *recipiente* contém zero ou mais itens, como mostra o diagrama abaixo:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entidades de conta Azure Cosmos" border="false":::

Pode ler mais sobre bases de dados, contentores e itens [aqui.](account-databases-containers-items.md) Algumas propriedades importantes são definidas ao nível do recipiente, entre *elas a chave de produção* e *partição.* 

A produção prevista é medida nas Unidades de Pedido *(RUs)* que têm um preço monetário e constituem um fator determinante substancial no custo de exploração da conta. A produção a provisionada pode ser selecionada em granularidade por contentor ou granularidade por base de dados, no entanto, a especificação de produção ao nível do contentor é normalmente preferível. Pode ler mais sobre o fornecimento de produção [aqui.](set-throughput.md)

À medida que os itens são inseridos num recipiente Cosmos DB, a base de dados cresce horizontalmente adicionando mais armazenamento e cálculo para lidar com pedidos. A capacidade de armazenamento e computação são adicionadas em *unidades discretas conhecidas como divisórias,* e deve escolher um campo nos seus documentos para ser a chave de partição que mapeia cada documento para uma partição. A forma como as divisórias são geridas é que cada divisória é atribuída a uma fatia aproximadamente igual fora da gama de valores-chave de partição; por isso, é aconselhável escolher uma chave de partição que seja relativamente aleatória ou distribuída uniformemente. Caso contrário, algumas divisórias verão substancialmente mais pedidos ( *partição quente* ), enquanto outras divisórias vêem substancialmente menos pedidos ( *partição fria),* e isso deve ser evitado. Pode aprender mais sobre a partilha [aqui.](partitioning-overview.md)

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documentos, tem de criar uma conta de API SQL com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicionar um contentor

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos clonar uma aplicação da SQL API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

```bash
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar para [Executar a aplicação](#run-the-app). 

### <a name="application-configuration-file"></a>Ficheiro de configuração de aplicação

Aqui mostramos como o Spring Boot e o Spring Data melhoram a experiência do utilizador - o processo de criação de um cliente Cosmos e a ligação aos recursos da Cosmos é agora config em vez de código. Na startup de aplicações, o Spring Boot trata de toda esta caldeira utilizando as definições em **aplicações.propriedades:**

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Assim que criar uma conta DB, base de dados e contentor Azure Cosmos, basta preencher os espaços em branco no ficheiro config e os Dados de Arranque/Mola de primavera farão automaticamente o seguinte: (1) criar uma instância Java SDK subjacente `CosmosClient` com o URI e a chave, e (2) ligar-se à base de dados e ao recipiente. Está tudo pronto, **sem mais código de gestão de recursos!**

### <a name="java-source"></a>Fonte java

O valor-add dos Dados de primavera também provém da sua interface simples, limpa, padronizada e independente da plataforma para operar nas datastores. Com base na amostra de Dados de primavera GitHub ligada acima, abaixo estão CRUD e amostras de consulta para manipular documentos DB Azure Cosmos com Spring Data Azure Cosmos DB.

* Criação de artigos e atualizações utilizando o `save` método.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Leituras de ponto utilizando o método de consulta derivado definido no repositório. O `findByIdAndLastName` desempenho executa leituras de ponto para `UserRepository` . Os campos mencionados no nome do método fazem com que os Dados de primavera executem uma leitura de ponto definida pelos `id` campos e `lastName` campos:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* O artigo elimina a `deleteAll` utilização:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Consulta derivada com base no nome do método do repositório. Os Dados de primavera implementam o `UserRepository` `findByFirstName` método como uma consulta JAVA SDK SQL no `firstName` campo (esta consulta não pôde ser implementada como uma leitura de ponto):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Executar a aplicação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e inicie a aplicação com as suas informações de ponto final. Isto permite à aplicação comunicar com a base de dados alojada.

1. Na janela de terminal do git, `cd` para a pasta de código de exemplo.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. Na janela do terminal de git, utilize o seguinte comando para instalar os pacotes DB de Dados de primavera Azure Cosmos necessários.

    ```bash
    mvn clean package
    ```

3. Na janela do terminal de git, utilize o seguinte comando para iniciar a aplicação DB de Dados de primavera Azure Cosmos:

    ```bash
    mvn spring-boot:run
    ```
    
4. A aplicação carrega **as propriedades.propriedades** e conecta os recursos na sua conta DB Azure Cosmos.
5. A aplicação realizará operações de ponto CRUD descritas acima.
6. A aplicação realizará uma consulta derivada.
7. A aplicação não apaga os seus recursos. Volte ao portal para [limpar os recursos](#clean-up-resources) da sua conta se quiser evitar incorrer em encargos.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta AZure Cosmos DB SQL API, criar uma base de dados de documentos e contentor utilizando o Data Explorer, e executar uma aplicação de Dados de primavera para fazer a mesma coisa programáticamente. Pode agora importar dados adicionais na sua conta DB Azure Cosmos. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).