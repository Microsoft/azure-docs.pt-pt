---
title: Ligar ao Azure Cosmos DB com a bússola
description: Saiba como utilizar a bússola do MongoDB para armazenar e gerir dados no Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 06/24/2019
author: roaror
ms.author: roaror
ms.openlocfilehash: ad302df3144787fd231166c7052ecfde268f4086
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451039"
---
# <a name="use-compass-to-connect-to-your-cosmos-db-account"></a>Utilize a bússola para ligar à sua conta do Cosmos DB

Este tutorial demonstra como usar [MongoDB bússola](https://www.mongodb.com/products/compass) quando o armazenamento de e/ou gestão de dados no Cosmos DB. Podemos usar o API do Azure Cosmos DB para o MongoDB para este passo a passo. Para aqueles que não estão familiarizados, bússola é uma GUI para MongoDB. Que é frequentemente utilizada para visualizar os seus dados, executar consultas ad-hoc, juntamente com a gerir os seus dados. 

O cosmos DB é um serviço da base de dados com múltiplos modelos distribuída globalmente da Microsoft. Pode criar e consultar documentos e bases de dados de gráfico, que beneficiam de capacidades de escalamento horizontal no núcleo do Cosmos DB e distribuição global chave/valor rapidamente.


## <a name="pre-requisites"></a>Pré-requisitos 
Para ligar à sua conta de Cosmos DB com Robo 3T, faça o seguinte:

* Transferir e instalar [bússola](https://www.mongodb.com/download-center/compass?jmp=hero)
* Ter seu Cosmos DB [cadeia de ligação](connect-mongodb-account.md) informações

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Ligar à API do Cosmos DB para MongoDB 
Para ligar a sua conta do Cosmos DB a bússola, pode seguir os passos abaixo:

1. Obter as informações de ligação para a sua conta do Cosmos configurada com o MongoDB de API do Azure Cosmos DB ao seguir as instruções [aqui](connect-mongodb-account.md).

    ![Captura de ecrã do painel de cadeia de ligação](./media/mongodb-compass/mongodb-compass-connection.png)

2. Clique no botão que diz **copiar para área de transferência** junto ao seu **cadeia de ligação primária/secundária** no Cosmos DB. Clicar neste botão irá copiar a cadeia de ligação de toda a sua área de transferência. 

    ![Captura de ecrã da cópia de botão de área de transferência](./media/mongodb-compass/mongodb-connection-copy.png)

3. Abra a bússola em seu ambiente de trabalho/máquina e clique em **Connect** e, em seguida, **ligar a...** . 

4. Compass irá detetar automaticamente uma ligação de área de transferência de cadeias de caracteres e pede-lhe para perguntar se pretende utilizá-la para ligar. Clique em **Sim** conforme mostrado na captura de ecrã abaixo.

    ![Captura de ecrã da linha de comandos de bússola para ligar](./media/mongodb-compass/mongodb-compass-detect.png)

5. Após clicar em **Sim** no passo anterior, os detalhes da cadeia de ligação serão automaticamente preenchidos. Remova o valor preenchido automaticamente na **nome do conjunto de réplicas** campo para garantir que isto é deixado em branco. 

    ![Captura de ecrã da linha de comandos de bússola para ligar](./media/mongodb-compass/mongodb-compass-replica.png)

6. Clique em **Connect** na parte inferior da página. Sua conta do Cosmos DB e as bases de dados devem agora estar visíveis na Bússola do MongoDB.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [utilizar Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para o MongoDB.
- Explorar o MongoDB [amostras](mongodb-samples.md) com a API do Azure Cosmos DB para o MongoDB.