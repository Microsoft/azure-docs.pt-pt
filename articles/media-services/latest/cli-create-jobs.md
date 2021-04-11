---
title: Exemplo de script do Azure CLI - Criar e submeter um emprego
description: Neste tópico, o script da CLI do Azure mostra como submeter uma Tarefa para uma Transformação de codificação simples com o URL de HTTPs.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3e05e3f67de03aa300fb650d23357286ee71c52
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960253"
---
# <a name="cli-example-create-and-submit-a-job"></a>Exemplo da CLI: Criar e submeter uma tarefa

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nos Serviços de Multimédia do Azure v3, quando submete tarefas para processar os seus vídeos, terá de informar os Serviços de Multimédia do Azure sobre onde se encontra o vídeo de entrada. Uma das opções é especificar um URL HTTPS como uma entrada de trabalho (como mostrado neste artigo). 

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Serviços de Comunicação](./account-create-how-to.md)Social.

## <a name="example-script"></a>Script de exemplo

Quando `az ams job start` correr, pode definir uma etiqueta na saída do trabalho. A etiqueta pode ser usada mais tarde para identificar para que é este ativo de saída. 

- Se atribuir um valor à etiqueta, desaprote de "-ativos de saída" para "nome de ativos=rótulo"
- Se não atribuir um valor à etiqueta, desaprote de "--ativos de saída" para "nome de ativos=".
  Note que adiciona "=" ao `output-assets` . . 

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

Obtém-se uma resposta semelhante a esta:

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

[az ams job (CLI)](/cli/azure/ams/job)
