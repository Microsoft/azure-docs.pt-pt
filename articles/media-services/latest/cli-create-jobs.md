---
title: Exemplo do Script da CLI do Azure – Criar e submeter uma tarefa | Microsoft Docs
description: Neste tópico, o script da CLI do Azure mostra como submeter uma Tarefa para uma Transformação de codificação simples com o URL de HTTPs.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 08caf4b78b1042d5753dba2bc73e93fa3a250a17
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122379"
---
# <a name="cli-example-create-and-submit-a-job"></a>Exemplo da CLI: Criar e enviar um trabalho

Nos Serviços de Multimédia do Azure v3, quando submete tarefas para processar os seus vídeos, terá de informar os Serviços de Multimédia do Azure sobre onde se encontra o vídeo de entrada. Uma das opções é especificar uma URL HTTPS como uma entrada de trabalho (como mostrado neste artigo). 

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

Ao executar `az ams job start`o, você pode definir um rótulo na saída do trabalho. O rótulo pode ser usado posteriormente para identificar o que é esse ativo de saída. 

- Se você atribuir um valor ao rótulo, defina '--output-assets ' como "assetname = rótulo"
- Se você não atribuir um valor ao rótulo, defina '--output-assets ' como "assetname =".
  Observe que você adiciona "=" ao `output-assets`. 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
```

Você Obtém uma resposta semelhante a esta:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

## <a name="next-steps"></a>Passos seguintes

[AZ AMS Job (CLI)](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
