---
title: Crie uma amostra de aplicação V4 Azure Cosmos DB Java SDK de ponta a ponta utilizando o Change Feed
description: Este guia acompanha-o através de uma simples aplicação API Java SQL que insere documentos num recipiente DB Azure Cosmos, mantendo uma visão materializada do recipiente utilizando o Change Feed.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 765fd3afc7fe688d3e6b0e3394e7dc8c39af69b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096857"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Como criar uma aplicação Java que usa Azure Cosmos DB SQL API e alterar processador de feed
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este guia de como guiar-o através de uma simples aplicação Java que usa a API API AZURE Cosmos DB SQL para inserir documentos num recipiente DB Azure Cosmos, mantendo ao mesmo tempo uma visão materializada do recipiente utilizando o Processador Change Feed e Change Feed. A aplicação Java comunica com o AZure Cosmos DB SQL API usando Azure Cosmos DB Java SDK v4.

> [!IMPORTANT]  
> Este tutorial é apenas para Azure Cosmos DB Java SDK v4. Por favor, veja as notas de lançamento Azure Cosmos DB Java SDK [v4,](sql-api-sdk-java-v4.md) [repositório Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4 dicas de [desempenho](performance-tips-java-sdk-v4-sql.md), e Azure Cosmos DB Java SDK v4 [guia de resolução de problemas](troubleshoot-java-sdk-v4-sql.md) para obter mais informações. Se está a utilizar uma versão mais antiga do que v4, consulte o guia [Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para ajudar a atualizar para v4.
>

## <a name="prerequisites"></a>Pré-requisitos

* O URI e a chave para a sua conta DB Azure Cosmos

* Maven

* Java 8

## <a name="background"></a>Fundo

O feed de alteração DB do Azure Cosmos fornece uma interface orientada para o evento para desencadear ações em resposta à inserção de documentos. Isto tem muitos usos. Por exemplo, em aplicações que são lidas e escrevem pesadas, o principal uso do feed de mudança é criar uma **visão materializada** em tempo real de um recipiente enquanto está a ingerir documentos. O recipiente de vista materializado irá conter os mesmos dados, mas divididos para leituras eficientes, tornando a aplicação tanto lida como escrita eficiente.

O trabalho de gestão de eventos de feed de mudança é amplamente tratado pela biblioteca de processadores de mudanças incorporada no SDK. Esta biblioteca é poderosa o suficiente para distribuir eventos de mudança de alimentos entre vários trabalhadores, se assim o desejar. Tudo o que tem de fazer é fornecer à biblioteca de feed de mudança uma chamada.

Este exemplo simples demonstra alterar a biblioteca do processador de alimentação com um único trabalhador a criar e a eliminar documentos de uma visão materializada.

## <a name="setup"></a>Configuração

Se ainda não o fez, clone o repo exemplo da aplicação:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Abra um terminal no diretório de repo. Construa a app executando

```bash
mvn clean package
```

## <a name="walkthrough"></a>Instruções

1. Como primeira verificação, deve ter uma conta DB Azure Cosmos. Abra o **portal Azure** no seu navegador, vá à sua conta DB Azure Cosmos e no painel esquerdo navegue para o **Data Explorer**.

   :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_empty.JPG" alt-text="Conta do Azure Cosmos DB":::

1. Executar a aplicação no terminal utilizando o seguinte comando:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. A imprensa entra quando vê

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    em seguida, volte ao portal Azure Data Explorer no seu navegador. Verá uma base de **dados A Loja De Dados Database** foi adicionada com três recipientes vazios: 

    * **InventárioContainer** - O registo de inventário da nossa mercearia exemplo, dividido em item ```id``` que é um UUID.
    * **InventárioContainer-pktype** - Uma visão materializada do registo de inventário, otimizada para consultas sobre item ```type```
    * **InventárioContainer-leases** - Um recipiente de locação é sempre necessário para alterar alimentos; os arrendamentos acompanham o progresso da app na leitura do feed de mudança.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG" alt-text="Recipientes vazios":::

1. No terminal, deve agora ver um pedido

    ```bash
    Press enter to start creating the materialized view...
    ```

    A imprensa entra. Agora, o seguinte bloco de código executará e rubricará o processador de alteração de alimentação noutro fio: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=InitializeCFP)]

    ```"SampleHost_1"``` é o nome do trabalhador do processador Change Feed. ```changeFeedProcessorInstance.start()``` é o que realmente inicia o processador Change Feed.

    Volte ao portal Azure Data Explorer no seu browser. No âmbito do contentor **Decontainer-leases,** clique em **itens** para ver o seu conteúdo. Verá que o Processador Change Feed povoou o recipiente de locação, ou seja, o processador atribuiu ao ```SampleHost_1``` trabalhador um arrendamento em algumas divisórias do **InventárioContainer**.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_leases.JPG" alt-text="Arrendamentos":::

1. A imprensa volta a entrar no terminal. Isto irá desencadear 10 documentos a serem inseridos no **InventárioContainer**. Cada inserção de documento aparece no feed de alteração como JSON; o seguinte código de retorno trata estes eventos espelhando os documentos JSON numa visão materializada:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=CFPCallback)]

1. Deixe o código funcionar 5-10sec. Em seguida, volte ao portal Azure Data Explorer e navegue para **InventárioContainer > itens**. Deve ver se os itens estão a ser inseridos no recipiente de inventário; note a chave de partição ```id``` ().

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_items.JPG" alt-text="Recipiente para alimentação":::

1. Agora, no Data Explorer navegue para **InventárioContainer-pktype > itens**. Esta é a vista materializada - os itens neste espelho de **contentorEs InventárioContainer** porque foram inseridos programáticamente por alteração de feed. Note a tecla de partição ```type``` (). Portanto, esta visão materializada é otimizada para consultas filtrando ```type``` sobre , o que seria ineficiente no **InventárioContainer** porque é dividido em ```id``` .

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG" alt-text="A screenshot mostra a página data Explorer para uma conta Azure Cosmos D B com itens selecionados.":::

1. Vamos apagar um documento tanto do **InventárioContainer** como do **InventárioContainer-pktype** usando apenas uma ```upsertItem()``` chamada. Primeiro, dê uma olhada no portal Azure Data Explorer. Vamos apagar o documento para o ```/type == "plums"``` qual; está cercado em vermelho abaixo

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG" alt-text="A screenshot mostra a página data Explorer para uma conta Azure Cosmos D B com um item específico I D selecionado.":::

    Introduza novamente para ligar a função ```deleteDocument()``` no código de exemplo. Esta função, mostrada abaixo, aumenta uma nova versão do documento com ```/ttl == 5``` , que define o documento Time-To-Live (TTL) a 5sec. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=DeleteWithTTL)]

    O feed de alteração ```feedPollDelay``` é definido para 100ms; portanto, o feed de alteração responde a esta atualização quase instantaneamente e as chamadas ```updateInventoryTypeMaterializedView()``` mostradas acima. Esta última chamada de função irá inserir o novo documento com TTL de 5sec em **InventárioContainer-pktype**.

    O efeito é que após cerca de 5 segundos, o documento expirará e será eliminado de ambos os recipientes.

    Este procedimento é necessário porque o feed de alteração apenas emite eventos na inserção ou atualização do item, e não na eliminação do item.

1. A imprensa entra mais uma vez para fechar o programa e limpar os seus recursos.
