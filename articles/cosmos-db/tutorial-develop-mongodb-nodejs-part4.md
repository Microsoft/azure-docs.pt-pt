---
title: NODE. js, aplicação Angular com a API de MongoB (Part4)
titleSuffix: Azure Cosmos DB
description: Parte 4 da série do tutorial sobre como criar uma aplicação MongoDB com Angular e Node no Azure Cosmos DB mediante a utilização das mesmas APIs que são utilizadas para MongoDB
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.openlocfilehash: 507fdd8566aca94ac4f027b938e833704c6e3e7d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548787"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-4-create-an-azure-cosmos-db-account"></a>Crie uma aplicação MongoDB com Angular e o Azure Cosmos DB - parte 4: Criar uma conta do Azure Cosmos DB

Este tutorial com várias parte demonstra como criar uma nova [conta de API do Azure Cosmos DB para o MongoDB](mongodb-introduction.md) aplicação escrita em node. js com Express, Angular e a base de dados do Azure Cosmos DB.

A Parte 4 do tutorial é a continuação da [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) e abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar um grupo de recursos do Azure com a CLI do Azure
> * Criar uma conta do Azure Cosmos DB com a CLI do Azure

## <a name="video-walkthrough"></a>Instruções de vídeo

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, certifique-se de que concluiu os passos na [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) do tutorial. 

Nesta secção do tutorial, pode utilizar a Azure Cloud Shell (no browser) ou a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) instalada localmente.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Este tutorial orienta-o ao longo dos passos para criar a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta do Azure Cosmos DB com o comando [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create).

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Em `<cosmosdb-name>`, utilize o nome exclusivo da sua conta do Azure Cosmos DB, o qual tem de ser único de entre todos os nomes de contas do Azure Cosmos DB no Azure.
* A definição `--kind MongoDB` permite que o Azure Cosmos DB tenha ligações de cliente MongoDB.

A conclusão do comando pode demorar alguns minutos. Quando terminar, a janela de terminal apresenta informações sobre a base de dados nova. 

Após a conta do Azure Cosmos DB ter sido criada:
1. Abra uma janela nova do browser e aceda a [https://portal.azure.com](https://portal.azure.com)
1. Clique no logótipo do Azure Cosmos DB ![Ícone do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) na barra do lado esquerdo, que lhe mostra todas as suas bases de dados do Azure Cosmos DB.
1. Clique na conta do Azure Cosmos DB que acabou de criar, selecione o separador **Descrição geral** e desloque-se para baixo para ver o mapa onde está localizada a base de dados. 

    ![Nova conta do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. Desloque-se para baixo na navegação do lado esquerdo e clique no separador **Replicar dados globalmente**, o que apresenta um mapa no qual pode ver as diferentes áreas em que pode replicar. Por exemplo, pode clicar em Sudeste da Austrália ou Leste da Austrália e replicar os dados na Austrália. Pode saber mais sobre a replicação global em [How to distribute data globally with Azure Cosmos DB](distribute-data-globally.md) (Como distribuir dados globalmente com o Azure Cosmos DB). Por agora, vamos apenas manter a única instância e, quando quisermos replicar, já sabemos como.

    ![Nova conta do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, fez o seguinte:

> [!div class="checklist"]
> * Criou um grupo de recursos do Azure com a CLI do Azure
> * Criou uma conta do Azure Cosmos DB com a CLI do Azure

Pode avançar para a parte seguinte do tutorial para ligar o Azure Cosmos DB à sua aplicação com o Mongoose.

> [!div class="nextstepaction"]
> [Use Mongoose to connect to Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md) (Utilizar o Mongoose para ligar ao Azure Cosmos DB)
