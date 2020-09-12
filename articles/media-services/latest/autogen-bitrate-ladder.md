---
title: Encode vídeos com Standard Encoder em Media Services - Azure / Microsoft Docs
description: Este tópico mostra como usar o Standard Encoder in Media Services para codificar um vídeo de entrada com uma escada de bitrate gerada automaticamente, com base na resolução de entrada e bitrate.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 05accd69f1868b8b0e0f6dbd4fb5c21ee843ec5e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297728"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Codificar com uma escada de bitrate gerada automaticamente

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Descrição geral

Este artigo explica como usar o Standard Encoder in Media Services para codificar um vídeo de entrada numa escada de bitrate gerada automaticamente (pares de resolução de bitrate) com base na resolução de entrada e bitrate. Esta definição de codificação incorporada, ou predefinida, nunca excederá a resolução de entrada e o bitrate. Por exemplo, se a entrada for de 720p a 3 Mbps, a produção permanece 720p na melhor das hipóteses, e começará com taxas inferiores a 3 Mbps.

### <a name="encoding-for-streaming"></a>Codificação para streaming

Quando utilizar a predefinição **AdaptiveStreaming** em **Transform,** obtém-se uma saída adequada para a entrega através de protocolos de streaming como HLS e DASH. Ao utilizar esta predefinição, o serviço determina inteligentemente quantas camadas de vídeo gerar e a que bitrate e resolução. O conteúdo de saída contém ficheiros MP4 onde o áudio codificado pela AAC e o vídeo codificado por H.264 não estão intercalados.

Para ver um exemplo de como esta predefinição é usada, consulte [stream a file](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Saída

Esta secção mostra três exemplos das camadas de vídeo de saída produzidas pelo codificadora Media Services como resultado da codificação com a predefinição **AdaptiveStreaming.** Em todos os casos, a saída contém um ficheiro MP4 apenas áudio com áudio estéreo codificado a 128 kbps.

### <a name="example-1"></a>Exemplo 1
Fonte com altura "1080" e framerate "29.970" produz 6 camadas de vídeo:

|Camada|Height|Width|Bitrate (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemplo 2
Fonte com altura "720" e framerate "23.970" produz 5 camadas de vídeo:

|Camada|Height|Width|Bitrate (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemplo 3
Fonte com altura "360" e framerate "29.970" produz 3 camadas de vídeo:

|Camada|Height|Width|Bitrate (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
