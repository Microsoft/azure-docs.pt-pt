---
title: Codificar vídeos com a Standard Encoder em Media Services - Azure Microsoft Docs
description: Este tópico mostra como usar o Standard Encoder em Media Services para codificar um vídeo de entrada com uma escada de bitrate gerada automaticamente, com base na resolução de entrada e bitrate.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c25c32f35adc1c017f0f4c012c82bd7e0af8d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733322"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Codificar com uma escada de bitrate gerada automaticamente

## <a name="overview"></a>Descrição geral

Este artigo explica como usar o Standard Encoder em Media Services para codificar um vídeo de entrada numa escada de bitrate gerada automaticamente (pares de resolução de bitrate) com base na resolução de entrada e bitrate. Esta definição codificadora incorporada, ou predefinida, nunca excederá a resolução de entrada e o bitrate. Por exemplo, se a entrada for de 720p a 3 Mbps, a saída permanece 720p na melhor das hipóteses, e começará a taxas inferiores a 3 Mbps.

### <a name="encoding-for-streaming"></a>Codificação para streaming

Quando utiliza o predefinido **AdaptiveStreaming** em **Transform,** obtém-se uma saída adequada para a entrega através de protocolos de streaming como HLS e DASH. Ao utilizar este preset, o serviço determina inteligentemente quantas camadas de vídeo gerar e em que bitrate e resolução. O conteúdo de saída contém ficheiros MP4 em que o áudio codificado a AAC e o vídeo codificado por H.264 não são intercaladas.

Para ver um exemplo de como este preset é usado, consulte [Stream um ficheiro](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Saída

Esta secção mostra três exemplos das camadas de vídeo de saída produzidas pelo codificador de Media Services como resultado da codificação com o pré-definido **AdaptiveStreaming.** Em todos os casos, a saída contém um ficheiro MP4 apenas áudio com áudio estéreo codificado a 128 kbps.

### <a name="example-1"></a>Exemplo 1
Fonte com altura "1080" e framerate "29.970" produz 6 camadas de vídeo:

|Camada|Altura|Largura|Bitrate (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemplo 2
Fonte com altura "720" e framerate "23.970" produz 5 camadas de vídeo:

|Camada|Altura|Largura|Bitrate (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemplo 3
Fonte com altura "360" e framerate "29.970" produz 3 camadas de vídeo:

|Camada|Altura|Largura|Bitrate (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
