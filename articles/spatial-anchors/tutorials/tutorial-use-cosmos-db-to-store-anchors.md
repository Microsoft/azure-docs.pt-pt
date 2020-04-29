---
title: 'Tutorial: Partilhar âncoras com a Azure Cosmos DB'
description: Neste tutorial, aprende-se a partilhar identificadores de Âncoras Espaciais Azure em dispositivos Android/iOS em Unidade com um serviço back-end e Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77615159"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Tutorial: Partilhar âncoras espaciais azure em sessões e dispositivos com um back back end Azure Cosmos

Este tutorial é uma continuação da partilha de [Âncoras Espaciais Azure em sessões e dispositivos.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Irá guiá-lo através do processo de adicionar mais algumas capacidades para fazer com que o Azure Cosmos DB sirva de armazenamento traseiro enquanto partilha âncoras espaciais azure em sessões e dispositivos.

![GIF ilustrando a persistência de objetos](./media/persistence.gif)

Vale a pena notar que, embora esteja a usar a Unity e a Azure Cosmos DB neste tutorial, é apenas para lhe dar um exemplo de como partilhar identificadores de Âncoras Espaciais em dispositivos. Pode usar outras línguas e tecnologias de back-end para atingir o mesmo objetivo. Além disso, a aplicação web ASP.NET Core utilizada neste tutorial requer o .NET Core 2.2 SDK. Funciona bem em Aplicações Web para Windows, mas atualmente não será executado em Web Apps para Linux.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Adicione uma Base de Dados Azure Cosmos ao grupo de recursos que criou anteriormente.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copie `Connection String` porque vai precisar.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Faça pequenas alterações nos ficheiros SharingService

No **Solution Explorer,** abra. `SharingService\Startup.cs`

Localize `#define INMEMORY_DEMO` no topo do ficheiro e comente a linha. Guarde o ficheiro.

No **Solution Explorer,** abra. `SharingService\appsettings.json`

Localize `StorageConnectionString` a propriedade e detetete `Connection String` o valor para ser o mesmo que o valor que copiou na base de [dados.](#create-a-database-account) Guarde o ficheiro.

Pode publicar novamente o Serviço de Partilha e executar a aplicação de amostras.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unity-20193"></a>Unidade 2019.3

Devido a alterações de rutura, a Unidade 2019.3 não é atualmente apoiada. Utilize a Unidade 2019.1 ou 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, usaste o Azure Cosmos DB para partilhar identificadores de âncora em dispositivos. Para saber mais sobre como usar âncoras espaciais Azure numa nova app Unity HoloLens, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Iniciar uma nova aplicação do Unity HoloLens](./tutorial-new-unity-hololens-app.md)
