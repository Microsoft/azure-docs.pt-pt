---
title: Como coser dois ou mais ficheiros de vídeo com .NET | Microsoft Docs
description: Este artigo mostra como costurar dois ou mais ficheiros de vídeo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282069"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Como costurar dois ou mais ficheiros de vídeo com .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Coser dois ou mais ficheiros de vídeo

O exemplo a seguir ilustra como pode gerar uma predefinição para costurar dois ou mais ficheiros de vídeo. O cenário mais comum é quando se quer adicionar um cabeçalho ou um trailer ao vídeo principal.

> [!NOTE]
> Os ficheiros de vídeo editados em conjunto devem partilhar propriedades (resolução de vídeo, taxa de fotogramas, contagem de faixas de áudio, etc.). Deve ter o cuidado de não misturar vídeos de diferentes taxas de fotogramas, ou com um número diferente de faixas de áudio.

## <a name="prerequisites"></a>Pré-requisitos

Clone ou descarregue as [amostras do Media Services .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/).  O código a seguir referenciado encontra-se na [pasta EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs).

### <a name="net-code"></a>.NET código

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
