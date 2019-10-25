---
title: Tutorial – compartilhar âncoras espaciais do Azure entre sessões e dispositivos com um back-end de Azure Cosmos DB | Microsoft Docs
description: Neste tutorial, você aprende a compartilhar identificadores de âncoras espaciais do Azure em dispositivos Android/iOS no Unity com um serviço de back-end e Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7ddbff563f79992f21aef5182177f4fb60c61dab
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882171"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Tutorial: compartilhando âncoras espaciais do Azure entre sessões e dispositivos com um back-end Azure Cosmos DB

Este tutorial é uma continuação do [compartilhamento de âncoras espaciais do Azure em sessões e dispositivos.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Ele vai orientá-lo pelo processo de adição de mais alguns recursos para fazer Azure Cosmos DB servirem como o armazenamento de back-end enquanto compartilham âncoras espaciais do Azure entre sessões e dispositivos.

![GIF ilustrando persistência de objeto](./media/persistence.gif)

Vale a pena observar que, embora você esteja usando o Unity e o Azure Cosmos DB neste tutorial, é só fornecer um exemplo de como compartilhar identificadores espaciais de âncoras entre dispositivos. Você pode fazer com que outras linguagens e tecnologias de back-end atinjam o mesmo objetivo. Além disso, o aplicativo Web ASP.NET Core usado neste tutorial requer o SDK do .NET Core 2,2. Ele funciona bem em aplicativos Web para o Windows, mas ele não é executado atualmente em aplicativos Web para Linux.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Adicione um banco de dados Cosmos do Azure ao grupo de recursos que você criou anteriormente. 

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copie o `Connection String` porque você precisará dele.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Fazer alterações secundárias nos arquivos SharingService

Em **Gerenciador de soluções**, abra `SharingService\Startup.cs`.

Localize `#define INMEMORY_DEMO` na parte superior do arquivo e comente essa linha. Salve o arquivo.

Em **Gerenciador de soluções**, abra `SharingService\appsettings.json`.

Localize a propriedade `StorageConnectionString` e defina o valor como o mesmo que o valor de `Connection String` que você copiou na [etapa criar uma conta de banco de dados](#create-a-database-account). Guarde o ficheiro.

Você pode publicar o serviço de compartilhamento novamente e executar o aplicativo de exemplo.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você usou Azure Cosmos DB para compartilhar identificadores de âncora entre dispositivos. Para saber mais sobre como usar as âncoras espaciais do Azure em um novo aplicativo de HoloLens do Unity, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Iniciando um novo aplicativo de HoloLens de Unity](./tutorial-new-unity-hololens-app.md)
