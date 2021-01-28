---
title: Stream de ficheiros de vídeo com o Azure Media Services CLI
description: Siga os passos deste tutorial para utilizar o Azure CLI para criar uma nova conta Azure Media Services, codificar um ficheiro e transmiti-lo ao Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: serviços de multimédia do azure, transmitir
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c78205d7e2b41628de9e8b92c9fa5506e82158cb
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954500"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---azure-cli"></a>Tutorial: Codificar um ficheiro remoto baseado em URL e transmitir o vídeo - Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tutorial mostra como codificar e transmitir facilmente vídeos numa variedade de navegadores e dispositivos utilizando o Azure Media Services e o Azure CLI. Pode especificar o conteúdo de entrada utilizando URLs HTTPS ou SAS ou caminhos para ficheiros no armazenamento Azure Blob.

O exemplo neste artigo codifica conteúdo que torna acessível através de um URL HTTPS. Atualmente, a Media Services v3 não suporta a codificação de transferências em fatias sobre URLs HTTPS.

No final deste tutorial, poderá transmitir um vídeo.  

![Reproduzir o vídeo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

Antes de encriptar, codificar, analisar, gerir e transmitir conteúdos de mídia no Azure, precisa de criar uma conta de Serviços de Comunicação Social. Essa conta deve estar associada a uma ou mais contas de armazenamento.

A sua conta de Media Services e todas as contas de armazenamento associadas devem estar na mesma subscrição do Azure. Recomendamos que utilize contas de armazenamento que se encontram no mesmo local que a conta dos Serviços de Comunicação Social para limitar os custos de latência e de saída de dados.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

```azurecli-interactive
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Neste exemplo, criamos uma conta LRS standard General-Purpose General-Purpose.

Se quiser experimentar contas de armazenamento, use `--sku Standard_LRS` . Quando estiver a escolher um SKU para produção, considere `--sku Standard_RAGRS` usar, que fornece replicação geográfica para a continuidade do negócio. Para mais informações, consulte [as contas de armazenamento.](/cli/azure/storage/account)

```azurecli-interactive
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Criar uma conta dos Media Services do Azure

```azurecli-interactive
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Obtém-se uma resposta como esta:

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-the-streaming-endpoint"></a>Iniciar o ponto final da transmissão em fluxo

O seguinte comando Azure CLI inicia o **ponto final de streaming** predefinido .

```azurecli-interactive
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Obtém-se uma resposta como esta:

```
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Se o ponto final de streaming já estiver em execução, obtém esta mensagem:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Criar uma transformação para codificação bitrate adaptativa

Crie uma **Transformação** para configurar tarefas comuns para codificar ou analisar vídeos. Neste exemplo, fazemos codificação bitrate adaptativa. Submetemos então um trabalho sob a transformação que criámos. O trabalho é o pedido aos Media Services para aplicar a transformação na entrada de conteúdo sonoro ou vídeo.

```azurecli-interactive
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Obtém-se uma resposta como esta:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Criar um elemento de saída

Crie um **Ativo** de saída para usar como saída do trabalho de codificação.

```azurecli-interactive
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Obtém-se uma resposta como esta:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-a-job-by-using-https-input"></a>Iniciar um trabalho utilizando a entrada HTTPS

Quando submete empregos para processar vídeos, tem de dizer aos Media Services onde encontrar o vídeo de entrada. Uma opção é especificar um URL HTTPS como a entrada de trabalho, como mostrado neste exemplo.

Quando `az ams job start` correr, pode definir uma etiqueta na saída do trabalho. Em seguida, pode utilizar a etiqueta para identificar para que serve o ativo de saída.

- Se atribuir um valor à etiqueta, desaprote de "-ativos de saída" para "assetname=label".
- Se não atribuir um valor à etiqueta, desaprote de "--ativos de saída" para "nome de ativos=".

  Note que adicionamos "=" ao `output-assets` .

```azurecli-interactive
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup
```

Obtém-se uma resposta como esta:

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

### <a name="check-status"></a>Verificar o estado

Em cinco minutos, verifique o estado do trabalho. Devia estar "Acabado". Ainda não está terminado, volte a verificar em alguns minutos. Quando estiver terminado, vá para o próximo passo e crie um **Localizador de Streaming.**

```azurecli-interactive
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Crie um localizador de streaming e obtenha um caminho

Depois de concluída a codificação, o próximo passo é disponibilizar o vídeo no ativo de saída aos clientes para reprodução. Para isso, primeiro crie um Localizador de Streaming. Em seguida, construa URLs de streaming que os clientes podem usar.

### <a name="create-a-streaming-locator"></a>Criar um localizador de transmissão

```azurecli-interactive
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Obtém-se uma resposta como esta:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Obtenha caminhos de localização de streaming

```azurecli-interactive
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Obtém-se uma resposta como esta:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Copie o caminho http live streaming (HLS). Neste caso, `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)` é.

## <a name="build-the-url"></a>Construir o URL

### <a name="get-the-streaming-endpoint-host-name"></a>Obtenha o nome do anfitrião do ponto final de streaming

```azurecli-interactive
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Copie o `hostName` valor. Neste caso, `amsaccount-usw22.streaming.media.azure.net` é.

### <a name="assemble-the-url"></a>Monte o URL

"https://" + &lt; valor do nome de anfitrião &gt;  +  &lt; Hls valor do caminho&gt;

Eis um exemplo:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Reproduzir o teste utilizando o Azure Media Player

> [!NOTE]
> Se um leitor estiver hospedado num site HTTPS, certifique-se de iniciar o URL com "https".

1. Abra um navegador web e vá para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Na caixa **URL,** cole o URL que construiu na secção anterior. Pode colar o URL em formato HLS, Dash ou Smooth. O Azure Media Player utilizará automaticamente um protocolo de streaming apropriado para a reprodução no seu dispositivo.
3. Selecione **Update Player**.

>[!NOTE]
>O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção.

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não precisar de nenhum dos recursos do seu grupo de recursos, incluindo os Serviços de Media e contas de armazenamento que criou para este tutorial, elimine o grupo de recursos.

Executar este comando Azure CLI:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Visão geral dos Serviços de Mídia](media-services-overview.md)
