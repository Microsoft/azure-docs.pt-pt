---
title: 'Tutorial: Partilhe âncoras com a Azure Cosmos DB'
description: Neste tutorial, aprende-se a partilhar identificadores Azure Spatial Anchors em dispositivos Android/iOS em Unidade com um serviço back-end e Azure Cosmos DB.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ff888cd98cc79f3e2d508b01f092102eaa038c86
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95494765"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Tutorial: Partilha de âncoras espaciais Azure em sessões e dispositivos com um Azure Cosmos DB back end

Este tutorial é uma continuação da partilha de [Âncoras Espaciais Azure em sessões e dispositivos.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Irá guiá-lo através do processo de adicionar mais algumas capacidades para fazer com que a Azure Cosmos DB sirva como o armazenamento back-end enquanto partilha âncoras espaciais Azure em sessões e dispositivos.

![GIF ilustrando persistência de objeto](./media/persistence.gif)

Vale a pena notar que, embora estejas a usar a Unidade e o Azure Cosmos DB neste tutorial, é apenas para te dar um exemplo de como partilhar identificadores de Âncoras Espaciais através de dispositivos. Pode usar outras idiomas e tecnologias de back-end para atingir o mesmo objetivo.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Adicione uma Base de Dados Azure Cosmos ao grupo de recursos que criou anteriormente.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copie o `Connection String` porque vai precisar.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Fazer pequenas alterações nos ficheiros Do SharingService

In **Solution Explorer,** abra `SharingService\Startup.cs` .

`#define INMEMORY_DEMO`Localize-se na parte superior do ficheiro e comente essa linha. Guarde o ficheiro.

In **Solution Explorer,** abra `SharingService\appsettings.json` .

Localize o `StorageConnectionString` imóvel e desa um valor igual ao `Connection String` valor que copiou na [criação de um passo de conta de base de dados.](#create-a-database-account) Guarde o ficheiro.

Pode publicar novamente o Serviço de Partilha e executar a aplicação da amostra.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, usou a Azure Cosmos DB para partilhar identificadores de âncora em dispositivos. Para saber mais sobre como usar âncoras espaciais Azure numa nova aplicação HoloLens da Unidade, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Iniciar uma nova aplicação do Unity HoloLens](./tutorial-new-unity-hololens-app.md)
