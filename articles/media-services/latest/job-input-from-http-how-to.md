---
title: Criar uma entrada de Trabalho de Serviços de Mídia Azure a partir de um URL HTTPS [ Microsoft Docs
description: Este tópico demonstra como criar uma entrada azure Media Services Job a partir de um URL HTTPS.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: a3d4568dd237491f28ae2880bdd78dd236870c3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74899835"
---
# <a name="create-a-job-input-from-an-https-url"></a>Criar uma entrada de trabalho a partir de um URL HTTPS

Nos Serviços de Multimédia do Azure v3, quando submete tarefas para processar os seus vídeos, terá de informar os Serviços de Multimédia do Azure sobre onde se encontra o vídeo de entrada. Uma das opções é especificar um URL HTTPS como entrada de trabalho (como mostra este exemplo). Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS. Para um exemplo completo, consulte esta [amostra GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Antes de começar a desenvolver, reveja [o Desenvolvimento com Os Serviços de Media v3 APIs](media-services-apis-overview.md) (inclui informações sobre o acesso a APIs, convenções de nomeação, etc.)

## <a name="net-sample"></a>amostra .NET

O código seguinte mostra como criar um trabalho com uma entrada DE URL HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [Códigos de Erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passos seguintes

[Crie uma entrada de trabalho a partir de um ficheiro local](job-input-from-local-file-how-to.md).
