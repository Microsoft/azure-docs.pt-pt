---
title: Ligue-se à Azure Cosmos DB usando a Bússola
description: Saiba como usar a Bússola MongoDB para armazenar e gerir dados em Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/05/2020
author: jasonwhowell
ms.author: jasonh
ms.openlocfilehash: c005c4fc15f7c7955a7254f386597eee01800da5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126605"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Utilize a Bússola MongoDB para ligar à API da Azure Cosmos DB para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este tutorial demonstra como usar a [Bússola MongoDB](https://www.mongodb.com/products/compass) ao armazenar e/ou gerir dados em Cosmos DB. Usamos a API da Azure Cosmos para a MongoDB para este walk-through. Para aqueles que não são familiares, a Bússola é um GUI para o MongoDB. É comumente usado para visualizar os seus dados, executar consultas ad-hoc, juntamente com a gestão dos seus dados.

Cosmos DB é o serviço de base de dados multi-modelo distribuído globalmente pela Microsoft. Pode criar e consultar rapidamente documentos, bases de dados chave/valor e gráficos, que beneficiam da distribuição global e das capacidades de escala horizontal no núcleo da Cosmos DB.

## <a name="pre-requisites"></a>Pré-requisitos

Para se ligar à sua conta Cosmos DB usando a Bússola MongoDB, deve:

* Descarregue e [instale a Bússola](https://www.mongodb.com/download-center/compass?jmp=hero)
* Tenha a sua [informação](connect-mongodb-account.md) sobre a cadeia de conexão Cosmos DB

> [!NOTE]
> Atualmente, a API da Azure Cosmos DB para a versão 3.2 do MongoDB Server é suportada com a versão mais recente da MongoDB Compass. A Bússola MongoDB ainda não está suportada com a versão do servidor 3.6. 

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Ligue-se à API da Cosmos DB para o MongoDB

Para ligar a sua conta Cosmos DB à Bússola, pode seguir os passos abaixo:

1. Recupere as informações de ligação da sua conta Cosmos configuradas com a API MongoDB da Azure Cosmos DB utilizando as instruções [aqui](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-connection.png" alt-text="Screenshot da lâmina de corda de ligação":::

2. Clique no botão que diz **Copiar para a área de transferência** ao lado da sua cadeia de **ligação primária/secundária** em Cosmos DB. Clicar neste botão irá copiar toda a sua cadeia de ligação para a sua área de transferência.

    :::image type="content" source="./media/mongodb-compass/mongodb-connection-copy.png" alt-text="Screenshot da lâmina de corda de ligação":::

3. Abrir a bússola no seu ambiente de trabalho/máquina e clicar em **Ligar** e, em seguida, **ligar a...** .

4. A bússola detetará automaticamente uma cadeia de ligação na área de transferência e solicitará a pergunta se deseja utilizar isso para ligar. Clique em **Sim** como mostrado na imagem abaixo.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-detect.png" alt-text="Screenshot da lâmina de corda de ligação":::

5. Ao clicar em **Sim** no passo acima, os seus dados a partir da cadeia de ligação serão automaticamente povoados. Remova o valor automaticamente povoado no campo **'set Name'** de réplica para garantir que fica em branco.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-replica.png" alt-text="Screenshot da lâmina de corda de ligação":::

6. Clique em **Ligar** na parte inferior da página. A sua conta e bases de dados cosmos DB devem agora ser visíveis dentro da Bússola MongoDB.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.
