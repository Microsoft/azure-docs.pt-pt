---
title: Tutorial - partilha Azure geográficos âncoras, entre as sessões e dispositivos | Documentos da Microsoft
description: Neste tutorial, saiba como partilhar os identificadores de âncora espaciais do Azure entre os dispositivos Android/iOS no Unity com um serviço de back-end.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 734e1d08413867a438270660fa97bb8c5737e087
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135387"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Tutorial: Partilhar as âncoras espaciais do Azure em sessões e dispositivos

Neste tutorial, irá aprender a utilizar [âncoras geográficos de Azure](../overview.md) criar âncoras durante uma sessão e, em seguida, localize-los, no mesmo dispositivo ou em outro. Também foi possível localizar estas âncoras mesmo por vários dispositivos no mesmo local e ao mesmo tempo.

![Persistência](./media/persistence.gif)

Âncoras espaciais do Azure é um serviço de programador para várias plataformas que permite-lhe criar experiências de realidade mista usando objetos que manter a respetiva localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, terá uma aplicação que pode ser implementada em duas ou mais dispositivos. Âncoras espaciais do Azure criado por uma instância pode ser partilhadas para os outros.

Vai aprender a:

> [!div class="checklist"]
> * Implemente uma aplicação Web do ASP.NET Core no Azure que pode ser utilizado para partilhar as âncoras, armazenando-os na memória durante um período de tempo.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro do exemplo de Unity de nossos inícios rápidos para tirar partido da aplicação Web âncoras de partilha.
> * Implemente e execute a um ou mais dispositivos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena observar que, embora usará Unity e uma aplicação Web do ASP.NET Core neste tutorial, é apenas mostrar um exemplo sobre como partilhar os identificadores de âncora espaciais do Azure em todos os outros dispositivos. Pode utilizar outros idiomas e as tecnologias de back-end para alcançar o mesmo objetivo. Além disso, a aplicação Web do ASP.NET Core utilizado neste Tutorial tem uma dependência no SDK do .NET Core 2.2. Ele é executado bem em aplicações Web do Azure normal (para Windows), mas atualmente não funciona em aplicações Web do Azure para Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-unity-sample-project"></a>Transfira o projeto de exemplo do Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Implementar o seu serviço de âncoras de partilha

Abra o Visual Studio e abra o projeto no `Sharing\SharingServiceSample` pasta.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, implementou uma aplicação Web do ASP.NET Core no Azure e, em seguida, configurado e implementado uma aplicação Unity. Criou âncoras geográficos com a aplicação e partilhou-los com outros dispositivos com a aplicação de Web do ASP.NET Core.

Para saber mais sobre como melhorar a sua aplicação Web do ASP.NET Core, para que ele use o Azure Cosmos DB para armazenar seus identificadores de âncora geográficos partilhados, avance para o próximo tutorial. O Azure Cosmos DB proporciona persistência a sua aplicação Web do ASP.NET Core. Ao fazê-lo por isso, irá permitir que a aplicação crie hoje uma âncora e volte dias mais tarde possa localizá-la novamente, utilizando o identificador de âncora armazenado na sua aplicação web.

> [!div class="nextstepaction"]
> [Tutorial: Utilizar o Azure Cosmos DB a Store âncoras](./tutorial-use-cosmos-db-to-store-anchors.md)
