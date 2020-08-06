---
title: Como criar uma sobreposição com o Media Encoder Standard
description: Saiba como criar uma sobreposição com o Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 85b1b6c56221deaa03057a7ccb658b4bf67124eb
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830585"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Como criar uma sobreposição com o Media Encoder Standard

O Media Encoder Standard permite-lhe sobrepor uma imagem a um vídeo existente. Atualmente, os seguintes formatos são suportados: png, jpg, gif e bmp.

## <a name="prerequisites"></a>Pré-requisitos

* Recolher as informações da conta que precisa para configurar o *appsettings.jsarquivado* na amostra. Se não sabe como fazê-lo, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](../../active-directory/develop/quickstart-register-app.md). Os valores seguintes são esperados no *appsettings.jsarquivado.*

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Se ainda não está familiarizado com a Transforms, recomenda-se que complete as seguintes atividades:

* Ler [vídeo e áudio da Codificação com Serviços de Media](encoding-concept.md)
* Leia [Como codificar com uma transformação personalizada - .NET](customize-encoder-presets-how-to.md). Siga os passos nesse artigo para configurar o .NET necessário para trabalhar com transformações e, em seguida, volte aqui para experimentar uma amostra predefinida sobreposta.
* Consulte o [documento de referência Transforms](/rest/api/media/transforms).

Uma vez que esteja familiarizado com transforms, descarregue a amostra sobreposta.

## <a name="overlays-preset-sample"></a>Sobreposiçãos amostra predefinida

Descarregue a [amostra de sobreposição de serviços de mídia](https://github.com/Azure-Samples/media-services-overlays) para começar com sobreposições.

## <a name="next-steps"></a>Passos seguintes

* [Subclip um vídeo ao codificar com os Media Services - .NET](subclip-video-dotnet-howto.md)