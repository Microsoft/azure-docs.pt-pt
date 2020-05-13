---
title: Crie uma amostra de aplicação de aplicação De ponta a ponta Azure Cosmos DB Java SDK v4 utilizando o Change Feed
description: Este guia percorre-o através de uma simples aplicação Java SQL API que insere documentos num recipiente Azure Cosmos DB, mantendo uma vista materializada do recipiente utilizando o Change Feed.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: 5e8656e891d250547174aa3deb27a94eebaa0ba3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125677"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Como criar uma aplicação Java que utiliza a API SQL Do Azure Cosmos DB e alterar processador de feed

> [!IMPORTANT]  
> Para mais informações sobre as dicas de desempenho do Azure Cosmos DB Java SDK v4, consulte as notas de lançamento do Azure Cosmos DB Java SDK v4, do [repositório Maven,](https://mvnrepository.com/artifact/com.azure/azure-cosmos)das dicas de [desempenho](performance-tips-java-sdk-v4-sql.md)do Azure Cosmos DB Java SDK v4 e do guia de resolução de [problemas](troubleshoot-java-sdk-v4-sql.md)Azure Cosmos DB Java SDK v4.
>

Este guia de como guiar percorre-o através de uma simples aplicação Java que utiliza a API Azure Cosmos DB SQL para inserir documentos num recipiente Azure Cosmos DB, mantendo uma visão materializada do recipiente utilizando o Processador Change Feed e Change Feed. A aplicação Java comunica com o Azure Cosmos DB SQL API utilizando o Azure Cosmos DB Java SDK v4.

## <a name="prerequisites"></a>Pré-requisitos

* O URI e a chave para a sua conta Azure Cosmos DB

* Maven

* Java 8

## <a name="background"></a>Segundo plano

O feed de mudança de DB Azure Cosmos fornece uma interface orientada para eventos para desencadear ações em resposta à inserção de documentos. Isto tem muitos usos. Por exemplo, em aplicações que são lidas e escrevem pesadas, um uso principal do feed de mudança é criar uma **visão materializada** em tempo real de um recipiente, uma vez que está a ingerir documentos. O recipiente de visualização materializado terá os mesmos dados, mas dividido para leituras eficientes, tornando a aplicação tanto lida como escrita eficiente.

O trabalho de gestão de eventos de feed de mudança é em grande parte tratado pela biblioteca de processadores de feed de mudança incorporada no SDK. Esta biblioteca é poderosa o suficiente para distribuir eventos de alimentação de mudança entre vários trabalhadores, se isso for desejado. Tudo o que tem que fazer é fornecer à biblioteca de mudanças uma chamada.

Este exemplo simples demonstra alterar a biblioteca de processadores de feed com um único trabalhador a criar e apagar documentos de uma visão materializada.

## <a name="setup"></a>Configuração

Se ainda não o fez, clone o exemplo da aplicação repo:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Abra um terminal no repo diretório. Construir a app executando

```bash
mvn clean package
```

## <a name="walkthrough"></a>Instruções

1. Como primeira verificação, deve ter uma conta Azure Cosmos DB. Abra o **portal Azure** no seu navegador, vá à sua conta Azure Cosmos DB e no painel esquerdo navegue para **o Data Explorer.**

    ![Conta Azure Cosmos DB](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Executar a aplicação no terminal utilizando o seguinte comando:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Pressione entrar quando vir

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    em seguida, volte ao portal Azure Data Explorer no seu navegador. Verá uma base de dados **MerceariaStoreDatabase** foi adicionada com três recipientes vazios: 

    * **InventárioRecipiente** - O registo de inventário para a nossa mercearia, dividido em item ```id``` que é um UUID.
    * **InventárioRecipiente-pktype** - Uma vista materializada do registo de inventário, otimizada para consultas sobre item```type```
    * **InventárioOs-arrendamentos -** Um recipiente de arrendamento é sempre necessário para a alimentação de mudanças; os arrendamentos acompanham o progresso da app na leitura do feed de mudança.


    ![Recipientes vazios](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. No terminal, deve agora ver um pedido.

    ```bash
    Press enter to start creating the materialized view...
    ```

    Pressione a entrada. Agora, o seguinte bloco de código executará e rubricará o processador de alimentação de alteração noutra linha: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for change feed processor start
    ```

    ```"SampleHost_1"```é o nome do processador Change Feed. ```changeFeedProcessorInstance.start()```é o que realmente inicia o processador Change Feed.

    Volte ao portal Azure Data Explorer no seu navegador. No recipiente de **aluguer de contentores de inventário,** clique **em itens** para ver o seu conteúdo. Verá que o Processador change feed tem povoado o recipiente de aluguer, ou seja, o processador atribuiu ao trabalhador um contrato de ```SampleHost_1``` arrendamento em algumas divisórias do **InventoryContainer**.

    ![Concessões](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Pressione a entrada novamente no terminal. Isto irá desencadear 10 documentos a serem inseridos no **InventoryContainer**. Cada inserção de documento aparece no feed de alteração como JSON; o seguinte código de chamada trata destes eventos espelhando os documentos JSON numa vista materializada:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

1. Deixe o código funcionar 5-10sec. Em seguida, volte ao portal Azure Data Explorer e navegue até ao **InventoryContainer > itens**. Deve ver que os itens estão a ser inseridos no recipiente de inventário; note a chave de partição ```id``` ().

    ![Recipiente de alimentação](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Agora, no Data Explorer navegue para **itens > de > de inventárioRecipiente.** Esta é a vista materializada - os itens neste recipiente espelho **InventárioContentor** porque foram inseridos programáticamente por change feed. Note a tecla de partição ```type``` (). Assim, esta vista materializada é otimizada para consultas de filtragem ```type``` , o que seria ineficiente no **InventoryContainer** porque é dividido em ```id``` .

    ![Vista materializada](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Vamos apagar um documento tanto do **InventoryContainer** como do **InventoryContainer-pktype** usando apenas uma ```upsertItem()``` chamada. Primeiro, dê uma olhada no portal Azure Data Explorer. Vamos apagar o documento para o ```/type == "plums"``` qual; está cercado em vermelho abaixo

    ![Vista materializada](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Introduza novamente a inserção para chamar a função ```deleteDocument()``` no código de exemplo. Esta função, mostrada abaixo, faz upserts uma nova versão do documento com , que define o ```/ttl == 5``` documento Time-To-Live (TTL) a 5sec. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    O feed de mudança ```feedPollDelay``` está definido para 100ms; portanto, alterar o feed responde a esta atualização quase instantaneamente e chamadas ```updateInventoryTypeMaterializedView()``` mostradas acima. Esta última chamada de função irá aumentar o novo documento com TTL de 5sec em **InventoryContainer-pktype**.

    O efeito é que, após cerca de 5 segundos, o documento expirará e será eliminado de ambos os recipientes.

    Este procedimento é necessário porque a alteração do feed apenas emite eventos sobre inserção ou atualização de itens, e não sobre a eliminação do item.

1. Pressione a indigitar mais uma vez para fechar o programa e limpar os seus recursos.
