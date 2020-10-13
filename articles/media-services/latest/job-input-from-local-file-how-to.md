---
title: Crie uma entrada de trabalho da Azure Media Services a partir de um ficheiro local Microsoft Docs
description: Este artigo demonstra como criar uma entrada de trabalho da Azure Media Services a partir de um ficheiro local.
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
ms.openlocfilehash: 02aaae89f7544392765b46361b1617e0fd7dae8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296812"
---
# <a name="create-a-job-input-from-a-local-file"></a>Criar uma entrada de trabalho a partir de um arquivo local

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nos Serviços de Multimédia do Azure v3, quando submete tarefas para processar os seus vídeos, terá de informar os Serviços de Multimédia do Azure sobre onde se encontra o vídeo de entrada. O vídeo de entrada pode ser armazenado como Um Ativo de Serviço de Mídia, caso em que cria um ativo de entrada baseado num ficheiro (armazenado localmente ou no armazenamento Azure Blob). Este tópico mostra como criar uma entrada de trabalho a partir de um ficheiro local. Para um exemplo completo, consulte esta [amostra do GitHub.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)

## <a name="prerequisites"></a>Pré-requisitos 

* [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.

## <a name="net-sample"></a>amostra .NET

O código que se segue mostra como criar um ativo de entrada e usá-lo como entrada para o trabalho. A função CreateInputAsset executa as seguintes ações:

* Cria o Ativo
* Obtém um [URL de SAS](../../storage/common/storage-sas-overview.md) gravável para o [contentor de armazenamento](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) do Elemento
* Carrega o ficheiro para o contentor de armazenamento através do URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

O seguinte código snippet cria um ativo de saída se já não existir:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

O seguinte código snippet submete um trabalho de codificação:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [códigos de erro](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passos seguintes

[Crie uma entrada de trabalho a partir de um URL HTTPS](job-input-from-http-how-to.md).
