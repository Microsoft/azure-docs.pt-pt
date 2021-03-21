---
title: Criar uma aplicação angular com API da Azure Cosmos DB para a MongoDB (Parte1)
description: Parte 4 da série do tutorial sobre como criar uma aplicação MongoDB com Angular e Node no Azure Cosmos DB mediante a utilização das mesmas APIs que são utilizadas para MongoDB
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 4ca7286676c441b2fa96883e0c187497f59d6222
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93082645"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Criar uma aplicação angular com API da Azure Cosmos DB para a MongoDB - Criar uma conta Cosmos
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este tutorial multi-partes demonstra como criar uma nova app escrita em Node.js com o Express e angular e, em seguida, conectá-la à sua [conta Cosmos configurada com a API da Cosmos DB para a MongoDB.](mongodb-introduction.md)

A Parte 4 do tutorial é a continuação da [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) e abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar um grupo de recursos do Azure com a CLI do Azure
> * Criar uma conta Cosmos utilizando o Azure CLI

## <a name="video-walkthrough"></a>Instruções de vídeo

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, certifique-se de que concluiu os passos na [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) do tutorial. 

Nesta secção do tutorial, pode utilizar a Azure Cloud Shell (no browser) ou a [CLI do Azure](/cli/azure/install-azure-cli) instalada localmente.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Este tutorial orienta-o ao longo dos passos para criar a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta DB Azure Cosmos com o [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) comando.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Em `<cosmosdb-name>`, utilize o nome exclusivo da sua conta do Azure Cosmos DB, o qual tem de ser único de entre todos os nomes de contas do Azure Cosmos DB no Azure.
* A definição `--kind MongoDB` permite que o Azure Cosmos DB tenha ligações de cliente MongoDB.

A conclusão do comando pode demorar alguns minutos. Quando terminar, a janela de terminal apresenta informações sobre a base de dados nova. 

Após a conta do Azure Cosmos DB ter sido criada:
1. Abra uma nova janela do navegador e vá para [https://portal.azure.com](https://portal.azure.com)
1. Clique no logótipo DB Azure Cosmos :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png"::: na barra esquerda e mostra-lhe todos os DBs Azure Cosmos que tem.
1. Clique na conta do Azure Cosmos DB que acabou de criar, selecione o separador **Descrição geral** e desloque-se para baixo para ver o mapa onde está localizada a base de dados. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png" alt-text="A screenshot mostra a visão geral de uma conta Azure Cosmos D B.":::

4. Desloque-se para baixo na navegação do lado esquerdo e clique no separador **Replicar dados globalmente**, o que apresenta um mapa no qual pode ver as diferentes áreas em que pode replicar. Por exemplo, pode clicar em Austrália Sudeste ou Leste da Austrália e replicar os dados na Austrália. Pode saber mais sobre a replicação global em [How to distribute data globally with Azure Cosmos DB](distribute-data-globally.md) (Como distribuir dados globalmente com o Azure Cosmos DB). Por agora, vamos apenas manter a única instância e, quando quisermos replicar, já sabemos como.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png" alt-text="A screenshot mostra uma conta Azure Cosmos D B com dados replicados globalmente selecionados.":::

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, fez o seguinte:

> [!div class="checklist"]
> * Criou um grupo de recursos do Azure com a CLI do Azure
> * Criou uma conta do Azure Cosmos DB com a CLI do Azure

Pode avançar para a parte seguinte do tutorial para ligar o Azure Cosmos DB à sua aplicação com o Mongoose.

> [!div class="nextstepaction"]
> [Use Mongoose to connect to Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md) (Utilizar o Mongoose para ligar ao Azure Cosmos DB)