---
title: Exemplos de código – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Saiba como usar os serviços cognitivas do Azure Content Moderator em seus aplicativos .NET por meio do SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 930571c841146e0b12efbf7325915ba2b23a7efa
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744341"
---
# <a name="content-moderator-net-sdk-samples"></a>Exemplos do SDK do .NET Content Moderator

A lista seguinte inclui ligações para exemplos de código criados com o SDK do Azure Content Moderator para .NET.

## <a name="moderation"></a>Moderação

- **Moderação de imagens**: [avalie uma imagem relativamente a conteúdo para adultos e indecoroso, texto e rostos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Consulte o guia de [início rápido do SDK do .net](dotnet-sdk-quickstart.md).
- **Imagens personalizadas**: [moderar com listas personalizadas de imagens](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Consulte o guia de[início rápido do SDK do .net](dotnet-sdk-quickstart.md).

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

- **Moderação de texto**: [texto de tela para dados pessoais e obscenas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Consulte o guia de[início rápido do SDK do .net](dotnet-sdk-quickstart.md).
- **Termos personalizados**: [modere com listas personalizadas de termos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Consulte o guia de [início rápido do SDK do .net](dotnet-sdk-quickstart.md).

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.
>

- **Moderação de vídeos**: [analise um vídeo relativamente a conteúdo para adultos e indecoroso, e obtenha resultados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Veja o [guia de início rápido](video-moderation-api.md).

## <a name="review"></a>Rever

- **Tarefas de imagem**: [inicie uma tarefa de moderação que analisa e cria revisões](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Veja o [guia de início rápido](moderation-jobs-quickstart-dotnet.md).
- **Revisões de imagem**: [crie as revisões para interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Veja o [guia de início rápido](dotnet-sdk-quickstart.md).
- **Revisões de vídeo**: [crie revisões de vídeos para interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Ver o [guia de início rápido](video-reviews-quickstart-dotnet.md)
- **Revisões de conversão de vídeo**: [crie revisões de conversão de vídeo para interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Ver o [guia de início rápido](video-reviews-quickstart-dotnet.md)

Veja todos os exemplos de .NET nos [exemplos de .NET do Content Moderator no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
