---
title: Transmitir arquivos de vídeo com os serviços de mídia do Azure e o CLI do Azure | Microsoft Docs
description: Siga as etapas deste tutorial para criar uma nova conta dos serviços de mídia do Azure, codificar um arquivo e transmiti-lo para Player de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: serviços de multimédia do azure, transmitir
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: ''
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 58193a94d09dee5df611acf5d98c8661dd18abbb
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639970"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---cli"></a>Tutorial: Codificar um arquivo remoto com base na URL e transmitir a video-CLI

Este tutorial mostra como codificar e transmitir vídeos facilmente em uma variedade de navegadores e dispositivos usando os serviços de mídia do Azure e o CLI do Azure. Você pode especificar o conteúdo de entrada usando URLs HTTPS ou SAS ou caminhos para arquivos no armazenamento de BLOBs do Azure.

O exemplo neste artigo codifica o conteúdo que você torna acessível por meio de uma URL HTTPS. No momento, os serviços de mídia v3 não dão suporte à codificação de transferência em partes sobre URLs HTTPS.

No final deste tutorial, você poderá transmitir um vídeo.  

![Reproduzir o vídeo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

Antes de poder criptografar, codificar, analisar, gerenciar e transmitir conteúdo de mídia no Azure, você precisa criar uma conta de serviços de mídia. Essa conta deve ser associada a uma ou mais contas de armazenamento.

Sua conta de serviços de mídia e todas as contas de armazenamento associadas devem estar na mesma assinatura do Azure. Recomendamos que você use contas de armazenamento que estejam no mesmo local que a conta dos serviços de mídia para limitar a latência e os custos de saída de dados.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Criar uma conta do Storage do Azure

Neste exemplo, criamos uma conta de LRS padrão v2 de uso geral.

Se você quiser experimentar as contas de armazenamento, use `--sku Standard_LRS`. Quando você estiver selecionando uma SKU para produção, considere `--sku Standard_RAGRS`o uso do, que fornece replicação geográfica para continuidade dos negócios. Para obter mais informações, consulte [contas de armazenamento](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Criar uma conta dos Azure Media Services

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Você Obtém uma resposta como esta:

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

O comando a seguir CLI do Azure inicia o **ponto de extremidade de streaming**padrão.

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Você Obtém uma resposta como esta:

```
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
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

Se o ponto de extremidade de streaming já estiver em execução, você receberá esta mensagem:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Criar uma transformação para a codificação de taxa de bits adaptável

Crie uma **transformação** para configurar tarefas comuns de codificação ou análise de vídeos. Neste exemplo, fazemos a codificação de taxa de bits adaptável. Em seguida, enviamos um trabalho na transformação que criamos. O trabalho é a solicitação aos serviços de mídia para aplicar a transformação para a entrada de conteúdo de vídeo ou áudio determinada.

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Você Obtém uma resposta como esta:

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

Crie um **ativo** de saída para usar como a saída do trabalho de codificação.

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Você Obtém uma resposta como esta:

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

## <a name="start-a-job-by-using-https-input"></a>Iniciar um trabalho usando a entrada HTTPS

Ao enviar trabalhos para processar vídeos, você precisa informar aos serviços de mídia onde encontrar o vídeo de entrada. Uma opção é especificar uma URL HTTPS como a entrada do trabalho, conforme mostrado neste exemplo.

Ao executar `az ams job start`o, você pode definir um rótulo na saída do trabalho. Você pode usar o rótulo para identificar o que é o ativo de saída.

- Se você atribuir um valor ao rótulo, defina '--output-assets ' como "assetname = rótulo".
- Se você não atribuir um valor ao rótulo, defina '--output-assets ' como "assetname =".

  Observe que adicionamos "=" ao `output-assets`.

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

Você Obtém uma resposta como esta:

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

Em cinco minutos, verifique o status do trabalho. Ele deve ser "concluído". Ele não foi concluído, verifique novamente em alguns minutos. Quando terminar, vá para a próxima etapa e crie um localizador de **streaming**.

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Criar um localizador de streaming e obter um caminho

Depois que a codificação for concluída, a próxima etapa será tornar o vídeo no ativo de saída disponível para os clientes para reprodução. Para fazer isso, primeiro crie um localizador de streaming. Em seguida, crie URLs de streaming que os clientes podem usar.

### <a name="create-a-streaming-locator"></a>Criar um localizador de transmissão

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Você Obtém uma resposta como esta:

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

### <a name="get-streaming-locator-paths"></a>Obter caminhos de localizador de streaming

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Você Obtém uma resposta como esta:

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

Copie o caminho HTTP Live streaming (HLS). Nesse caso, `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`é.

## <a name="build-the-url"></a>Compilar a URL 

### <a name="get-the-streaming-endpoint-host-name"></a>Obter o nome do host do ponto de extremidade de streaming

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```
Copie o `hostName` valor. Nesse caso, `amsaccount-usw22.streaming.media.azure.net`é.

### <a name="assemble-the-url"></a>Montar a URL

"https://" + &lt;valor&gt; + donomedo hostHLSvalordocaminho&lt;&gt;

Segue-se um exemplo:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Reprodução de teste usando Player de Mídia do Azure

> [!NOTE]
> Se um player estiver hospedado em um site HTTPS, certifique-se de iniciar a URL com "https".

1. Abra um navegador da Web e vá [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)para.
2. Na caixa **URL** , Cole a URL que você criou na seção anterior. Você pode colar a URL no formato HLS, Dash ou Smooth. Player de Mídia do Azure usará automaticamente um protocolo de streaming apropriado para reprodução em seu dispositivo.
3. Selecione **Atualizar Player**.

>[!NOTE]
>O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de nenhum dos recursos em seu grupo de recursos, incluindo os serviços de mídia e as contas de armazenamento que você criou para este tutorial, exclua o grupo de recursos.

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

[Visão geral dos serviços de mídia](media-services-overview.md)

