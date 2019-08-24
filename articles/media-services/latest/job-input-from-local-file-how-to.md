---
title: Criar uma entrada de trabalho dos serviços de mídia do Azure de um arquivo local | Microsoft Docs
description: Este tópico mostra como criar uma entrada de trabalho de um arquivo local.
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
ms.openlocfilehash: 94ac332867d46deb426672fbc40cc72cc121fe64
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998141"
---
# <a name="create-a-job-input-from-a-local-file"></a>Criar uma entrada de trabalho de um arquivo local

Nos Serviços de Multimédia do Azure v3, quando submete tarefas para processar os seus vídeos, terá de informar os Serviços de Multimédia do Azure sobre onde se encontra o vídeo de entrada. O vídeo de entrada pode ser armazenado como um ativo de serviço de mídia. nesse caso, você cria um ativo de entrada com base em um arquivo (armazenado localmente ou no armazenamento de BLOBs do Azure). Este tópico mostra como criar uma entrada de trabalho de um arquivo local. Para obter um exemplo completo, consulte este [exemplo do GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Exemplo do .NET

O código a seguir mostra como criar um ativo de entrada e usá-lo como a entrada para o trabalho. A função CreateInputAsset executa as seguintes ações:

* Cria o ativo
* Obtém um [URL de SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) gravável para o [contentor de armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) do Elemento
* Carrega o ficheiro para o contentor de armazenamento através do URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

O trecho de código a seguir criará um ativo de saída se ele ainda não existir:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAssetAsync)]

O trecho de código a seguir envia um trabalho de codificação:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Códigos de erro das tarefas

Consulte [códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passos Seguintes

[Crie uma entrada de trabalho de uma URL https](job-input-from-http-how-to.md).
