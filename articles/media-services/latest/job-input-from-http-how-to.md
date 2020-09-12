---
title: Crie uma entrada de trabalho de trabalho azure media a partir de um URL HTTPS / Microsoft Docs
description: Este tópico demonstra como criar uma entrada de trabalho de serviços de mídia Azure a partir de um URL HTTPS.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: e440e4393065d2bc1cad1a96b4f8c975624faa11
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295517"
---
# <a name="create-a-job-input-from-an-https-url"></a>Criar uma entrada de trabalho a partir de um URL HTTPS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nos Serviços de Multimédia do Azure v3, quando submete tarefas para processar os seus vídeos, terá de informar os Serviços de Multimédia do Azure sobre onde se encontra o vídeo de entrada. Uma das opções é especificar um URL HTTPS como uma entrada de trabalho (como mostrado neste exemplo). Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS. Para um exemplo completo, consulte esta [amostra do GitHub.](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)

> [!TIP]
> Antes de começar a desenvolver, [reveja o Desenvolvimento com os Serviços de Comunicação Social v3 APIs](media-services-apis-overview.md) (inclui informações sobre o acesso a APIs, convenções de nomeação, etc.)

## <a name="net-sample"></a>amostra .NET

O código que se segue mostra como criar um trabalho com uma entrada URL HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [códigos de erro](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Próximos passos

[Crie uma entrada de trabalho a partir de um ficheiro local.](job-input-from-local-file-how-to.md)
