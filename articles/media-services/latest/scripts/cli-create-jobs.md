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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 63d036ea4faaf7e24f337fa3956986d165c84854
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244349"
---
# <a name="cli-example-create-and-submit-a-job"></a>Exemplo da CLI: Criar e submeter uma tarefa

Em serviços de multimédia v3, quando submete tarefas para processar os seus vídeos, terá de informar os serviços de multimédia onde encontrar o vídeo de entrada. Uma das opções é especificar um URL HTTPS como uma tarefa de entrada (como mostrado neste artigo). 

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

Quando executa `az ams job start`, pode definir uma etiqueta de saída da tarefa. A etiqueta mais tarde pode ser utilizada para identificar o que este recurso de saída destina-se. 

- Se atribuir um valor para a etiqueta, defina '--recursos de saída para "assetname = etiqueta"
- Se não atribuir um valor para a etiqueta, defina '--recursos de saída para "assetname =".
  Observe que adicione "=" para o `output-assets`. 

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

Obter uma resposta semelhante ao seguinte:

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

## <a name="next-steps"></a>Passos Seguintes

Para obter mais exemplos, veja [Azure CLI samples](../cli-samples.md) (Exemplos de CLI do Azure).
