---
title: Tutorial - uma amostra de aplicação Async Java SQL API de ponta a ponta com Change Feed
description: Este tutorial acompanha-o através de uma simples aplicação Java SQL API que insere documentos num recipiente Azure Cosmos DB, mantendo uma vista materializada do recipiente utilizando o Change Feed.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586702"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Tutorial - uma amostra de aplicação Async Java SQL API de ponta a ponta com Change Feed

Este guia tutorial acompanha-o através de uma simples aplicação Java SQL API que insere documentos num recipiente Azure Cosmos DB, mantendo uma vista materializada do recipiente utilizando o Change Feed.

## <a name="prerequisites"></a>Pré-requisitos

* Computador pessoal

* O URI e a chave para a sua conta Azure Cosmos DB

* Maven

* Java 8

## <a name="background"></a>Segundo plano

O Azure Cosmos DB Change Feed fornece uma interface orientada para eventos para desencadear ações em resposta à inserção de documentos. Isto tem muitos usos. Por exemplo, em aplicações que são lidas e escrevem pesadas, um uso principal do Change Feed é criar uma **visão materializada** em tempo real de um contentor, uma vez que está a ingerir documentos. O recipiente de visualização materializado terá os mesmos dados, mas dividido para leituras eficientes, tornando a aplicação tanto lida como escrita eficiente.

O trabalho de gestão de eventos change feed é em grande parte tratado pela biblioteca do Processador Change Feed incorporado no SDK. Esta biblioteca é poderosa o suficiente para distribuir eventos change feed entre vários trabalhadores, se isso for desejado. Tudo o que tem que fazer é fornecer à biblioteca Change Feed uma chamada.

Este exemplo simples demonstra a biblioteca do Processador Change Feed com um único trabalhador a criar e apagar documentos de uma visão materializada.

## <a name="setup"></a>Configuração

Se ainda não o fez, clone o exemplo da aplicação repo:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> Você tem uma escolha para trabalhar através deste Quickstart com Java SDK 4.0 ou Java SDK 3.7.0. **Se quiser utilizar Java SDK 3.7.0, no ```git checkout SDK3.7.0```tipo terminal **. Caso contrário, mantenha-se no ```master``` ramo, que não se aplica a Java SDK 4.0.

Abra um terminal no repo diretório. Construir a app executando

```bash
mvn clean package
```

## <a name="walkthrough"></a>Instruções

1. Como primeira verificação, deve ter uma conta Azure Cosmos DB. Abra o **Portal Azure** no seu navegador, vá à sua conta Azure Cosmos DB e no painel esquerdo navegue para **o Data Explorer.**

    ![Conta Azure Cosmos DB](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Executar a aplicação no terminal utilizando o seguinte comando:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Pressione entrar quando vir

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    em seguida, volte ao Azure Portal Data Explorer no seu navegador. Verá uma base de dados **MerceariaStoreDatabase** foi adicionada com três recipientes vazios: 

    * **InventárioRecipiente** - O registo de inventário para a nossa ```id``` mercearia, dividido em item que é um UUID.
    * **InventárioRecipiente-pktype** - Uma vista materializada do registo de inventário, otimizada para consultas sobre item```type```
    * **InventárioDe-arrendamentos -** Um recipiente de arrendamento é sempre necessário para o Change Feed; os arrendamentos acompanham o progresso da app na leitura do Change Feed.


    ![Recipientes vazios](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. No terminal, deve agora ver um pedido.

    ```bash
    Press enter to start creating the materialized view...
    ```

    Pressione a entrada. Agora, o seguinte bloco de código executará e rubricará o processador Change Feed noutra linha: 


    **Java SDK 4.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

    **Java SDK 3.7.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start    
    ```

    ```"SampleHost_1"```é o nome do processador Change Feed. ```changeFeedProcessorInstance.start()```é o que realmente inicia o processador Change Feed.

    Volte ao Azure Portal Data Explorer no seu navegador. No recipiente de **aluguer de contentores de inventário,** clique **em itens** para ver o seu conteúdo. Verá que o Processador change feed tem povoado o recipiente de ```SampleHost_1``` aluguer, ou seja, o processador atribuiu ao trabalhador um contrato de arrendamento em algumas divisórias do **InventoryContainer**.

    ![Concessões](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Pressione a entrada novamente no terminal. Isto irá desencadear 10 documentos a serem inseridos no **InventoryContainer**. Cada inserção de documento aparece no Feed de Alteração como JSON; o seguinte código de chamada trata destes eventos espelhando os documentos JSON numa vista materializada:

    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```

1. Deixe o código funcionar 5-10sec. Em seguida, volte ao Azure Portal Data Explorer e navegue até **inventáriocontentor > itens**. Deve ver que os itens estão a ser inseridos no recipiente de inventário; note a chave```id```de partição ().

    ![Recipiente de alimentação](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Agora, no Data Explorer navegue para **itens > de > de inventárioRecipiente.** Esta é a vista materializada - os itens neste recipiente espelho **InventárioContainer** porque foram inseridos programáticamente pela Change Feed. Note a tecla```type```de partição (). Assim, esta vista materializada é otimizada ```type```para consultas de filtragem , o que ```id```seria ineficiente no **InventoryContainer** porque é dividido em .

    ![Vista materializada](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Vamos apagar um documento tanto do **InventoryContainer** como do **InventoryContainer-pktype** usando apenas uma ```upsertItem()``` chamada. Primeiro, dê uma olhada no Azure Portal Data Explorer. Eliminaremos o documento ```/type == "plums"```para o qual; está cercado em vermelho abaixo

    ![Vista materializada](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Introduza novamente a ```deleteDocument()``` inserção para chamar a função no código de exemplo. Esta função, mostrada abaixo, faz upserts ```/ttl == 5```uma nova versão do documento com , que define o documento Time-To-Live (TTL) a 5sec. 
    
    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
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

    O Change ```feedPollDelay``` Feed está definido para 100ms; portanto, change Feed responde a esta atualização ```updateInventoryTypeMaterializedView()``` quase instantaneamente e chamadas mostradas acima. Esta última chamada de função irá aumentar o novo documento com TTL de 5sec em **InventoryContainer-pktype**.

    O efeito é que, após cerca de 5 segundos, o documento expirará e será eliminado de ambos os recipientes.

    Este procedimento é necessário porque o Change Feed apenas emite eventos sobre inserção ou atualização de itens, e não sobre a eliminação do item.

1. Pressione a indigitar mais uma vez para fechar o programa e limpar os seus recursos.
