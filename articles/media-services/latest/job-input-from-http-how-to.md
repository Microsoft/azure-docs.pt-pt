---
title: Criar uma entrada da tarefa de serviços de multimédia do Azure a partir de um URL HTTPS | Documentos da Microsoft
description: Este tópico mostra como criar uma entrada da tarefa a partir de um URL de HTTP (s).
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
ms.openlocfilehash: f6eee912bb3bba112bd13969f1a8d9cb5748e387
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413818"
---
# <a name="create-a-job-input-from-an-https-url"></a>Criar uma entrada da tarefa a partir de um URL HTTPS

Nos Serviços de Multimédia do Azure v3, quando submete tarefas para processar os seus vídeos, terá de informar os Serviços de Multimédia do Azure sobre onde se encontra o vídeo de entrada. Uma das opções é especificar um URL HTTPS como uma tarefa de entrada (como mostrado neste exemplo). Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS. Para obter um exemplo completo, consulte [exemplo de GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Antes de começar a desenvolver, reveja [desenvolver com os serviços de multimédia v3 APIs](media-services-apis-overview.md) (inclui informações sobre como aceder a APIs, as convenções de nomenclatura, etc.)

## <a name="net-sample"></a>Exemplo de .NET

O código a seguir mostra como criar uma tarefa com um URL HTTPS de entrada.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passos Seguintes

[Criar uma entrada da tarefa a partir de um ficheiro local](job-input-from-local-file-how-to.md).
