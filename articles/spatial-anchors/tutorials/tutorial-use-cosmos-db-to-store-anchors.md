---
title: Tutorial - partilha entre sessões e dispositivos com as âncoras espaciais do Azure e um back-end do Azure Cosmos DB | Documentos da Microsoft
description: Neste tutorial, saiba como partilhar os identificadores de âncora espaciais do Azure entre os dispositivos de Android/iOS no Unity com um serviço de back-end e o Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: b585b13f40be447a5c5a4b348efc28bf5171e210
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863190"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Tutorial: Partilha entre sessões e dispositivos com as âncoras espaciais do Azure e um back-end do Azure Cosmos DB

Este tutorial mostra-lhe como utilizar [âncoras geográficos de Azure](../overview.md) para:

1. Criar âncoras numa sessão e, em seguida, localize-los em outra sessão no dispositivo idêntica ou diferente. Por exemplo, em outro dia.
2. Crie âncoras que podem estar localizadas por vários dispositivos no mesmo local, ao mesmo tempo.

![Persistência](./media/persistence.gif)

[Azure âncoras geográficos](../overview.md) é um serviço de programador para várias plataformas que permite a criação de realidade mista experiências de uso de objetos que manter a respetiva localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, terá uma aplicação que pode ser implementada em duas ou mais dispositivos. Âncoras espaciais do Azure criado por uma instância irá partilhar os seus identificadores para os outros com o Cosmos DB.

Vai aprender a:

> [!div class="checklist"]
> * Implemente uma aplicação Web do ASP.NET Core no Azure que pode ser utilizado para partilhar as âncoras, armazenando-os no Cosmos DB.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro do exemplo de Unity de nossos inícios rápidos para tirar partido da aplicação Web âncoras de partilha.
> * Implemente e execute a um ou mais dispositivos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena observar que, embora usará Unity e o Azure Cosmos DB neste tutorial, é apenas mostrar um exemplo sobre como partilhar os identificadores de âncora espaciais do Azure em todos os outros dispositivos. Pode utilizar outros idiomas e as tecnologias de back-end para alcançar o mesmo objetivo. Além disso, a aplicação Web do ASP.NET Core utilizado neste Tutorial tem uma dependência no SDK do .NET Core 2.2. Ele é executado bem em aplicações Web do Azure normal (para Windows), mas atualmente não funciona em aplicações Web do Azure para Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Anote o `Connection String` como será utilizado mais tarde.

## <a name="open-the-sample-project-in-unity"></a>Abra o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Implementar o seu serviço de âncoras de partilha

Abra o Visual Studio e abra o projeto no `Sharing\SharingServiceSample` pasta.

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>Configurar o serviço para que ele use o Cosmos DB

Na **Explorador de soluções**, abra `SharingService\Startup.cs`.

Localize o `#define INMEMORY_DEMO` linha na parte superior do ficheiro e comentá-lo. Guarde o ficheiro.

Na **Explorador de soluções**, abra `SharingService\appsettings.json`.

Localize a `StorageConnectionString` propriedade e defina o valor para ser o `Connection String` que demorou a nota no [criar um passo de conta de base de dados](#create-a-database-account). Guarde o ficheiro.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou a Azure Cosmos DB para partilhar os identificadores de âncora entre dispositivos. Para saber mais sobre a biblioteca de âncoras espaciais do Azure, avance para o nosso guia sobre como criar e localizar as âncoras.

> [!div class="nextstepaction"]
> [Criar e localizar as âncoras usando âncoras espaciais do Azure](../create-locate-anchors-overview.md)
