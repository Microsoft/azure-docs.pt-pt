---
title: Criar um contributo de emprego da Azure Media Services a partir de um arquivo local Microsoft Docs
description: Este artigo demonstra como criar uma entrada de emprego da Azure Media Services a partir de um ficheiro local.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: c5acda0ccec409ec06d0f3f2226b9819e3f130c7
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538418"
---
# <a name="create-a-job-input-from-a-local-file"></a>Criar uma entrada de emprego a partir de um arquivo local

Nos Serviços de Multimédia do Azure v3, quando submete tarefas para processar os seus vídeos, terá de informar os Serviços de Multimédia do Azure sobre onde se encontra o vídeo de entrada. O vídeo de entrada pode ser armazenado como um Media Service Asset, caso em que cria um ativo de entrada com base num ficheiro (armazenado localmente ou no armazenamento do Azure Blob). Este tópico mostra como criar uma entrada de emprego a partir de um arquivo local. Para um exemplo completo, consulte esta [amostra GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>amostra .NET

O código seguinte mostra como criar um ativo de entrada e usá-lo como entrada para o trabalho. A função CreateInputAsset executa as seguintes ações:

* Cria o Ativo
* Obtém um [URL de SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) gravável para o [contentor de armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) do Elemento
* Carrega o ficheiro para o contentor de armazenamento através do URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

O seguinte código snippet cria um ativo de saída se já não existir:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

O seguinte código snippet apresenta um trabalho de codificação:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [Códigos de Erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passos seguintes

[Crie uma entrada de trabalho a partir de um URL HTTPS](job-input-from-http-how-to.md).
