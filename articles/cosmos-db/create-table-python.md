---
title: 'Quickstart: Tabela API com Python - Azure Cosmos DB'
description: Este guia de introdução mostra como utilizar a API de Tabela do Azure Cosmos DB para criar uma aplicação com o portal do Azure e Python
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-python-october2019
ms.openlocfilehash: 673155706c3b7d44b41562c2dc8ce24a94ac6a58
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083636"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Guia de Introdução: Criar uma aplicação de API de Tabela com Python e Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

Neste arranque rápido, cria-se e gere-se uma conta API de Mesa Azure Cosmos DB a partir do portal Azure, e do Visual Studio com uma aplicação Python clonada do GitHub. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, basede-chaves e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure. Você também pode usar o [Emulador Db Azure Cosmos](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Visual Studio 2019,](https://www.visualstudio.com/downloads/)com o **desenvolvimento azure** e as cargas de trabalho de **desenvolvimento python** selecionadas durante a configuração. 
- [Git.](https://git-scm.com/downloads)

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
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Em seguida, abra o ficheiro da solução no Visual Studio. 

> ! [DICA] Para uma passagem mais detalhada de código semelhante, consulte o artigo da amostra da [Tabela Cosmos DB API.](table-storage-how-to-use-python.md)

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite à aplicação comunicar com a base de dados alojada. 

1. Na sua conta Azure Cosmos DB no [portal Azure,](https://portal.azure.com/)selecione **Connection String**. 

    ![Ver e copiar o FIO DE LIGAÇÃO nas definições de fio de ligação](./media/create-table-python/view-and-copy-connection-string-in-connection-string-settings.png)

2. Copie o NOME DA CONTA com o botão do lado direito.

3. Abra o ficheiro *config.py* e cola o NOME DE CONTA do portal para o valor STORAGE_ACCOUNT_NAME na linha 19.

4. Volte ao portal e copie a CHAVE PRIMÁRIA.

5. Cole a CHAVE PRIMÁRIA do portal no valor STORAGE_ACCOUNT_KEY na linha 20.

6. Guarde o ficheiro *config.py.*

## <a name="run-the-app"></a>Executar a aplicação

1. No Estúdio Visual, clique no projeto no **Solution Explorer.**

2. Selecione o ambiente python atual e, em seguida, clique à direita.

2. **Selecione Instalar o Pacote Python**e, em seguida, *introduza*a tabela de armazenamento azul .

3. Prima F5 para executar a aplicação. A aplicação é apresentada no browser. 

Agora pode voltar ao Data Explorer e ver, consultar, modificar e trabalhar com estes novos dados. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Cosmos DB, criar uma tabela usando o Data Explorer, e executar uma aplicação Python no Visual Studio para adicionar dados de tabela.  Agora, pode consultar os dados com a API de Tabela.  

> [!div class="nextstepaction"]
> [Importar dados da tabela para a API de Tabela](table-import.md)
