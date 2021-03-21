---
title: Upgrade vídeo ao vivo Analytics em IoT Edge de 1.0 a 2.0
description: Este artigo cobre as diferenças e as diferentes coisas a ter em conta ao atualizar o live video analytics (LVA) no módulo Azure IoT Edge.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955654"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Upgrade vídeo ao vivo Analytics em IoT Edge de 1.0 a 2.0

Este artigo cobre as diferenças e as diferentes coisas a ter em conta ao atualizar o live video analytics (LVA) no módulo Azure IoT Edge.

## <a name="change-list"></a>Lista de Alterações

> [!div class="mx-tdCol4BreakAll"]
> |Título|Vídeo ao vivo Analytics 1.0|Vídeo ao vivo Analytics 2.0|Description|
> |-------------|----------|---------|---------|
> |Imagem de recipiente|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Microsoft publicou imagens de estivadores para live video analytics em Azure IoT Edge|
> |**Nódes media gráficos** |    |   |   |
> |Origens|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Fonte RTSP </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Fonte de mensagem do hub IoT |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Fonte RTSP </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Fonte de mensagem do hub IoT | Nós do MediaGraph que funcionam como fontes para ingestão de meios e mensagens.|
> |Processadores|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de deteção de movimentos </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de filtro de taxa de fotogramas </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de extensão http </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de extensão Grpc </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador do portão de sinal |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de deteção de movimentos </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**Processador de filtro de taxa de fotogramas**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de extensão http </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de extensão Grpc </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador do portão de sinal | Nós do MediaGraph que lhe permitem formatar os meios antes de enviar para servidores de inferência de IA.|
> |Pias|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Pia de ativos </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sumidouro de Arquivo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Pia de mensagem IoT Hub|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Pia de ativos </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sumidouro de Arquivo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Pia de mensagem IoT Hub| Nós do MediaGraph que lhe permitem armazenar os meios processados.|
> |**MediaGraphs suportados** |    |   |   |
> |Topologias|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação contínua de vídeo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de Movimento e Gravação contínua de Vídeo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise Externa e Gravação contínua de Vídeo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em eventos em Movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em eventos em IA</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em eventos em eventos externos </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de Movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de Movimento seguida de inferencing de IA |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação contínua de vídeo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de Movimento e Gravação contínua de Vídeo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise Externa e Gravação contínua de Vídeo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em eventos em Movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em eventos em IA</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em eventos em eventos externos </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de Movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de Movimento seguida de inferencing de IA </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Composição da IA** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Gravação de Áudio e Vídeo** </br>  | Topologias de MediaGraph que lhe permitem definir a planta de um gráfico, com parâmetros como espaços reservados para valores.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>Upgrade do módulo Live Video Analytics no IoT Edge de 1.0 a 2.0
Ao atualizar o Live Video Analytics no módulo IoT Edge, certifique-se de atualizar as seguintes informações.
### <a name="container-image"></a>Imagem de recipiente
No seu modelo de implementação, procure o seu vídeo ao vivo analítico no módulo IoT Edge sob o `modules` nó e atualize o `image` campo como:
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> Se não modificou o nome do live video analytics no módulo IoT Edge, procure `lvaEdge` por baixo do nó do módulo.

### <a name="topology-file-changes"></a>Alterações no Ficheiro topologia
Nos seus ficheiros de topologia, certifique-se de que **`apiVersion`** está definido para 2.0

### <a name="mediagraph-node-changes"></a>Alterações no nó do nó do MediaGraph


* O áudio da sua fonte de câmara pode agora ser passado a jusante juntamente com o vídeo. Pode passar **apenas áudio** ou **apenas vídeo** ou áudio e **vídeo, ambos** com a ajuda de qualquer nó **`outputSelectors`** gráfico. Por exemplo, se pretender selecionar o vídeo apenas a partir da fonte RTSP e passá-lo a jusante, adicione o seguinte ao nó de origem RTSP:
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>É **`outputSelectors`** uma propriedade opcional. Se isto não for utilizado, o gráfico de mídia passará o áudio (se ativado) e o vídeo da câmara RTSP a jusante. 

* In `MediaGraphHttpExtension` e `MediaGraphGrpcExtension` processadores, note as seguintes alterações:  
    #### <a name="image-properties"></a>Propriedades de imagem
    * `MediaGraphImageFormatEncoded` já não é apoiado. 
      * Em vez disso, use **`MediaGraphImageFormatBmp`** **`MediaGraphImageFormatJpeg`** ou ou . **`MediaGraphImageFormatPng`** . Por exemplo,
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * Se quiser utilizar imagens RAW, utilize **`MediaGraphImageFormatRaw`** . Para utilizar isto, atualize o nó de imagem como:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > Os valores possíveis do pixelFormat incluem: `yuv420p` , , , , , , , , `rgb565be` , `rgb565le` `rgb555be` `rgb555le` `rgb24` `bgr24` `argb` `rgba` `abgr` , `bgra`  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>extensãoConfiguration para processador de extensão Grpc  
    * No `MediaGraphGrpcExtension` processador, está disponível uma nova propriedade **`extensionConfiguration`** chamada, que é uma cadeia opcional que pode ser usada como parte do contrato gRPC. Este campo pode ser usado para transmitir quaisquer dados para o servidor de inferência e pode definir como o servidor de inferência utiliza esses dados.  
    Um caso de uso desta propriedade é quando você tem vários modelos de IA embalados em um único servidor de inferência. Com esta propriedade não precisará expor um nó para cada modelo de IA. Em vez disso, para uma instância de gráfico, como um fornecedor de extensão, você pode definir como selecionar os diferentes modelos de IA usando a propriedade e durante a **`extensionConfiguration`** execução, LVA passará esta cadeia para o servidor de inferenculação, que pode usá-lo para invocar o modelo de IA desejado.  

    #### <a name="ai-composition"></a>Composição da IA
    * Live Video Analytics 2.0 suporta agora a utilização de mais de um processador de extensão de gráficos de mídia dentro de uma topologia. Pode passar os quadros de mídia da câmara RTSP para diferentes modelos de IA, quer sequencialmente, em paralelo ou numa combinação de ambos. Por favor, veja uma topologia de amostras mostrando dois modelos de IA sendo usados sequencialmente.

### <a name="disk-space-management-with-sink-nodes"></a>Gestão do espaço do disco com nódoas de pia
* No nó **da pia do ficheiro,** pode agora especificar quanto espaço em disco o módulo Live Video Analytics no IoT Edge pode utilizar para armazenar as imagens processadas. Para tal, adicione o **`maximumSizeMiB`** campo ao nó FileSink. Um nó de sumidouro de ficheiro de amostra é o seguinte:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* No nó **da pia do Ativo,** pode especificar quanto espaço em disco o módulo Live Video Analytics no IoT Edge pode utilizar para armazenar as imagens processadas. Para tal, adicione o **`localMediaCacheMaximumSizeMiB`** campo ao nó de Pia de Ativos. Um nó de pia de medida é o seguinte:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  O caminho **da pia do Arquivo** é dividido em trajetória de diretório base e padrão de nome de ficheiro, enquanto o caminho da pia do **Ativo** inclui o caminho do diretório base.  

### <a name="frame-rate-management"></a>Gestão da Taxa de Fotogramas
* **`MediaGraphFrameRateFilterProcessor`** é depreciado em Live Video Analytics no módulo **IoT Edge 2.0.**
    * Para amostrar o vídeo de entrada para processamento, adicione a **`samplingOptions`** propriedade aos processadores de extensão MediaGraph `MediaGraphHttpExtension` (ou `MediaGraphGrpcExtension` )  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Métricas de módulos no formato Prometheus usando Telegraf
Com esta libertação, a Telegraf pode ser usada para enviar métricas para o Azure Monitor. A partir daí, as métricas podem ser direcionadas para o Log Analytics ou para um centro de eventos.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="Taxonomia dos eventos":::

Pode produzir uma imagem Telegraf com uma configuração personalizada facilmente utilizando o docker. Saiba mais na página [de Monitorização e Registo.](monitoring-logging.md#azure-monitor-collection-via-telegraf)

## <a name="next-steps"></a>Passos seguintes

[Começar com Live Video Analytics no IoT Edge](get-started-detect-motion-emit-events-quickstart.md)