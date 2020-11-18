---
title: Como criar uma sobreposição com o Media Encoder Standard
description: Saiba como criar uma sobreposição com o Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 743fe146042c7b52394cc4ee8ced49a0f540e79c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844289"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Como criar uma sobreposição com o Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Media Encoder Standard permite-lhe sobrepor uma imagem, ficheiro áudio ou outro vídeo para outro vídeo. A entrada deve especificar exatamente um ficheiro. Pode especificar um ficheiro de imagem no formato JPG, PNG, GIF ou BMP, ou num ficheiro áudio (como um ficheiro WAV, MP3, WMA ou M4A) ou num ficheiro de vídeo.


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
    "Location": "",
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
