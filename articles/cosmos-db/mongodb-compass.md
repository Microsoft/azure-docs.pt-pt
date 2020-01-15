---
title: Conectar-se a Azure Cosmos DB usando a bússola
description: Saiba como usar a bússola do MongoDB para armazenar e gerenciar dados no Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 06/24/2019
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: 5c01bd02b1fef7c7b27a3119465cad53155b9bbf
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940981"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Usar a bússola do MongoDB para se conectar à API do Azure Cosmos DB para MongoDB 

Este tutorial demonstra como usar a [bússola do MongoDB](https://www.mongodb.com/products/compass) ao armazenar e/ou gerenciar dados no cosmos DB. Usamos a API do Azure Cosmos DB para MongoDB para este passo a passo. Para aqueles de sua familiaridade, a bússola é uma GUI para MongoDB. Normalmente, ele é usado para visualizar seus dados, executar consultas ad hoc, juntamente com o gerenciamento de seus dados. 

Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Você pode criar e consultar rapidamente documentos, chave/valor e bancos de dados de grafo, todos beneficiando-se dos recursos de escala horizontal e distribuição global no núcleo de Cosmos DB.


## <a name="pre-requisites"></a>Pré-requisitos 
Para se conectar à sua conta do Cosmos DB usando Robo 3T, você deve:

* Baixar e instalar a [bússola](https://www.mongodb.com/download-center/compass?jmp=hero)
* Ter suas informações de [cadeia de conexão](connect-mongodb-account.md) Cosmos DB

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Conectar-se à API do Cosmos DB para MongoDB 
Para conectar sua conta do Cosmos DB à bússola, você pode seguir as etapas abaixo:

1. Recupere as informações de conexão para sua conta do cosmos configurada com a API do MongoDB Azure Cosmos DB usando as instruções [aqui](connect-mongodb-account.md).

    ![Captura de tela da folha cadeia de conexão](./media/mongodb-compass/mongodb-compass-connection.png)

2. Clique no botão que diz **copiar para a área de transferência** ao lado de sua **cadeia de conexão primária/secundária** no cosmos DB. Clicar nesse botão copiará toda a cadeia de conexão para a área de transferência. 

    ![Captura de tela do botão Copiar para área de transferência](./media/mongodb-compass/mongodb-connection-copy.png)

3. Abra a bússola em sua área de trabalho/computador e clique em **conectar** e, em seguida, **conectar-se a...** . 

4. A bússola detectará automaticamente uma cadeia de conexão na área de transferência e solicitará que você queira usá-la para se conectar. Clique em **Sim** , conforme mostrado na captura de tela abaixo.

    ![Captura de tela do prompt da bússola para se conectar](./media/mongodb-compass/mongodb-compass-detect.png)

5. Depois de clicar em **Sim** na etapa anterior, os detalhes da cadeia de conexão serão preenchidos automaticamente. Remova o valor preenchido automaticamente no campo **nome do conjunto de réplicas** para garantir que ele seja deixado em branco. 

    ![Captura de tela do prompt da bússola para se conectar](./media/mongodb-compass/mongodb-compass-replica.png)

6. Clique em **conectar** na parte inferior da página. Sua conta de Cosmos DB e os bancos de dados agora devem estar visíveis na bússola do MongoDB.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Explore os [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.