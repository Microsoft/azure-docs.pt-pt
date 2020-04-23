---
title: Use a Tabela API e Java para construir uma app - Azure Cosmos DB
description: Este guia de introdução mostra como utilizar a API de Tabela do Azure Cosmos DB para criar uma aplicação com o portal do Azure e Java
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: java
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: e3517804cb66a9f98351e4c68f4f7c4387cee8fe
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083806"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-table-api-data"></a>Quickstart: Construa uma app Java para gerir dados da API da Tabela Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

Neste arranque rápido, cria-se uma conta API de Mesa Azure Cosmos DB, e utiliza-se o Data Explorer e uma aplicação Java clonada do GitHub para criar tabelas e entidades. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, basede-chaves e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure. Você também pode usar o [Emulador Db Azure Cosmos](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- Kit de [Desenvolvimento Java (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte `JAVA_HOME` a sua variável ambiental para a pasta onde o JDK está instalado.
- Um [arquivo binário Maven.](https://maven.apache.org/download.cgi) 
- [Git.](https://www.git-scm.com/downloads) 

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

> [!IMPORTANT] 
> Tem de criar uma nova conta da API de Tabela para trabalhar com os SDKs de API de Tabela geralmente disponíveis. As contas de API de Tabela criadas durante a pré-visualização não são suportadas pelos SDKs geralmente disponíveis.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adicionar uma tabela

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação de Tabela a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente.

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

> ! [DICA] Para uma passagem mais detalhada de código semelhante, consulte o artigo da amostra da [Tabela Cosmos DB API.](table-storage-how-to-use-java.md) 

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite à aplicação comunicar com a base de dados alojada. 

1. Na sua conta Azure Cosmos DB no [portal Azure,](https://portal.azure.com/)selecione **Connection String**. 

   ![Ver as informações de cadeia de ligação no painel de cordas de ligação](./media/create-table-java/cosmos-db-quickstart-connection-string.png)

2. Copie a CADEIA DE LIGAÇÃO PRIMÁRIA com o botão de cópia do lado direito.

3. Abra *config.properties* a partir da pasta *C:\git-samples\storage-table-java-getting-started\src\main\resources.* 

5. Comente a linha um e anule os comentários da linha dois. As primeiras duas linhas devem agora ter este aspeto.

    ```xml
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. Cole a sua CADEIA DE LIGAÇÃO PRIMÁRIA do portal no valor de StorageConnectionString na linha 2. 

    > [!IMPORTANT]
    > Se o Ponto final utilizar documents.azure.com, significa que tem uma conta de pré-visualização, e tem de criar uma [nova conta de API de Tabela](#create-a-database-account) para trabalhar com o SDK de API de Tabela geralmente disponível.
    >

7. Guarde o ficheiro *config.properties.*

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="run-the-app"></a>Executar a aplicação

1. Na janela de terminal do git, `cd` na pasta storage-table-java-getting-started.

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. Na janela do terminal git, execute os seguintes comandos para executar a aplicação Java.

    ```git
    mvn compile exec:java 
    ```

    A janela da consola apresenta os dados da tabela adicionados para a nova base de dados de tabela no Azure Cosmos DB.

    Agora pode voltar ao Data Explorer e ver, consultar, modificar e trabalhar com estes novos dados. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Cosmos DB, criar uma tabela usando o Data Explorer, e executar uma app Java para adicionar dados de tabela.  Agora, pode consultar os dados com a API de Tabela.  

> [!div class="nextstepaction"]
> [Importar dados da tabela para a API de Tabela](table-import.md)
