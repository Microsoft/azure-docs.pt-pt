---
title: Tutorial - partilha Azure geográficos âncoras nas sessões e dispositivos com o Azure Cosmos DB back-end | Documentos da Microsoft
description: Neste tutorial, saiba como partilhar os identificadores de âncoras espaciais do Azure em todos os dispositivos Android/iOS no Unity com um serviço de back-end e o Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 19c2298c9bda03acba28496a97c89c2a53e3c44e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964909"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Tutorial: Partilha Azure geográficos âncoras nas sessões e dispositivos com o Azure Cosmos DB back-end

Neste tutorial, irá aprender a utilizar [âncoras geográficos de Azure](../overview.md) criar âncoras durante uma sessão e, em seguida, localize-los durante a sessão de outro, no mesmo dispositivo ou em outro. Por exemplo, a segunda sessão pode ser em outro dia. Também foi possível localizar estas âncoras mesmo por vários dispositivos no mesmo local e ao mesmo tempo.

![Persistência de objeto que ilustram GIF](./media/persistence.gif)

[Azure âncoras geográficos](../overview.md) é um serviço de programador para várias plataformas que pode utilizar para criar experiências de realidade mista com objetos que manter a respetiva localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, terá uma aplicação que pode ser implementada em duas ou mais dispositivos. Âncoras geográficos criadas por uma instância irão partilhar os seus identificadores com os outros através da utilização do Azure Cosmos DB.

Vai aprender a:

> [!div class="checklist"]
> * Implemente uma aplicação web do ASP.NET Core no Azure que pode ser utilizado para partilhar as âncoras, armazenando-os no Azure Cosmos DB.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo no exemplo Unity dos inícios rápidos do Azure para tirar partido da aplicação Web âncoras de partilha.
> * Implementar uma aplicação para um ou mais dispositivos e executá-lo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena observar que, embora usará Unity e o Azure Cosmos DB neste tutorial, é apenas dar um exemplo de como partilhar os identificadores de âncoras geográficos em todos os dispositivos. Pode utilizador outros idiomas e as tecnologias de back-end para alcançar o mesmo objetivo. Além disso, a aplicação web ASP.NET Core utilizada neste tutorial requer o SDK do .NET Core 2.2. Ele é executado bem em aplicações Web para Windows, mas atualmente não será executado nas aplicações Web para Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copiar o `Connection String` porque irá precisar das mesmas.

## <a name="open-the-sample-project-in-unity"></a>Abra o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Implementar o serviço de âncoras de partilha

Abra o Visual Studio e abrir o projeto no `Sharing\SharingServiceSample` pasta.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Configurar o serviço para utilizar a sua base de dados do Azure Cosmos DB

Na **Explorador de soluções**, abra `SharingService\Startup.cs`.

Localize `#define INMEMORY_DEMO` na parte superior do ficheiro e comentário que fora de linha. Guarde o ficheiro.

Na **Explorador de soluções**, abra `SharingService\appsettings.json`.

Localize a `StorageConnectionString` propriedade e defina o valor seja igual a `Connection String` valor que copiou no [criar um passo de conta de base de dados](#create-a-database-account). Guarde o ficheiro.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou a Azure Cosmos DB para partilhar os identificadores de âncora entre dispositivos. Para saber mais sobre como utilizar as âncoras espaciais do Azure numa nova aplicação Unity HoloLens, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [A partir de uma nova aplicação Android](./tutorial-new-unity-hololens-app.md)