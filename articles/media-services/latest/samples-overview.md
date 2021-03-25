---
title: Exemplos dos Serviços de Multimédia v3
description: Este artigo contém uma lista de todas as amostras disponíveis para os Media Services v3 organizados por método e SDK.  As amostras incluem .NET, Node.JS, Python e Java, bem como REST com Carteiro.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: bfe39020da0be245f47d0c954de7598914d6534d
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105051345"
---
# <a name="media-services-v3-samples"></a>Exemplos dos Serviços de Multimédia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo contém uma lista de todas as amostras disponíveis para serviços de mídia organizados por método e SDK.  As amostras incluem .NET, Node.JS, Python e Java, bem como REST com Carteiro.

## <a name="rest-postman-collection"></a>Coleção REST Postman

As amostras [do REST Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman) incluem um ambiente e uma recolha do Carteiro para que possa importar para o cliente do Carteiro.

## <a name="samples-by-sdk"></a>Amostras por SDK

Encontrará descrição e ligações às amostras que pode estar procurando em cada um dos separadores.

## <a name="net"></a>[.NET](#tab/net/)

| Pasta | Description |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|Como submeter um trabalho usando uma predefinição incorporada e uma entrada HTTP URL, publicar o ativo de saída para streaming e baixar os resultados para verificação.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|Como submeter um trabalho usando uma predefinição de codificação personalizada H.264 e uma entrada HTTP URL, publicar o ativo de saída para streaming e baixar os resultados para verificação.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|Como submeter um trabalho usando uma predefinição de codificação HEVC personalizada e uma entrada HTTP URL, publicar o ativo de saída para streaming e baixar resultados para verificação.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|Como submeter um emprego usando o JobInputSequence para coser 2 ou mais ativos que podem ser cortados por tempo de início ou fim. O ficheiro codificado resultante é um único vídeo com todos os ativos costurados.  A amostra também publicará o ativo de saída para streaming e resultados de descarregamento para verificação.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|Como submeter um trabalho usando uma predefinição personalizada com um sprite de miniatura e uma entrada HTTP URL, publicar o ativo de saída para streaming e baixar os resultados para verificação.|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|Como criar um LiveEvent com um arquivo completo até 25 horas e um filtro no ativo com janela de 5 minutos de DVR. Como utilizar um filtro para criar um localizador para streaming.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Como criar um analisador de vídeo transformar, carregar um ficheiro de vídeo para um ativo de entrada, submeter um trabalho com a transformação e descarregar os resultados para verificação.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Como criar um analisador de áudio transformar, carregar um ficheiro de media para um ativo de entrada, submeter um trabalho com a transformação e descarregar os resultados para verificação.|
| [Proteção de Conteúdos/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Como criar uma transformação com predefinição adaptiveStreaming incorporada, submeter um trabalho, criar uma ContentKeyPolicy usando uma chave secreta, associar o ContentKeyPolicy com o StreamingLocator, obter um token e imprimir um url para reprodução no Azure Media Player. Quando um stream é solicitado por um jogador, os Media Services utilizam a chave especificada para encriptar dinamicamente o seu conteúdo com AES-128 e o Azure Media Player utiliza o símbolo para desencriptar.|
| [ConteúdoProteção/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Como criar uma transformação com predefinição adaptiveStreaming incorporada, submeter um trabalho, criar uma configuração ContentKeyPolicy com Widevine usando uma chave secreta, associar o ContentKeyPolicy com o StreamingLocator, obter um token e imprimir um url para reprodução num Jogador De Largura. Quando um utilizador solicita conteúdo protegido por Widevine, a aplicação do jogador solicita uma licença do serviço de licença dos Serviços de Comunicação Social. Se a aplicação de leitor tiver autorização, o serviço de licenças dos Serviços de Multimédia emite uma licença para o leitor. Uma licença Widevine contém a chave de desencriptação que pode ser usada pelo leitor cliente para desencriptar e transmitir o conteúdo.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Como criar uma transformação com predefinição adaptiveStreaming incorporada, submeter um trabalho, criar uma configuração ContentKeyPolicy com PlayReady usando uma chave secreta, associar o ContentKeyPolicy com o StreamingLocator, obter um token e imprimir um url para reprodução num Azure Media Player. Quando um utilizador solicita conteúdo protegido pela PlayReady, a aplicação do jogador solicita uma licença do serviço de licença dos Media Services. Se a aplicação de leitor tiver autorização, o serviço de licenças dos Serviços de Multimédia emite uma licença para o leitor. Uma licença PlayReady contém a chave de desencriptação que pode ser usada pelo leitor cliente para desencriptar e transmitir o conteúdo.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|Como encriptar dinamicamente o seu conteúdo com a PlayReady e a Widevine DRM e reproduzir o conteúdo sem solicitar uma licença do serviço de licença. Mostra como criar uma transformação com predefinição adaptivestreaming incorporada, submeter um trabalho, criar uma ContentKeyPolicy com restrição aberta e configuração persistente playReady/Widevine, associar o ContentKeyPolicy a um StreamingLocator e imprimir um url para reprodução.|
| [Streaming/Filtros de Ativos](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Como criar uma transformação com predefinição adaptivestreaming incorporada, submeter um trabalho, criar um filtro de ativos e um filtro de conta, associar os filtros aos localizadores de streaming e urls de impressão para reprodução.|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Como criar uma transformação com predefinição adaptivestreaming incorporada, submeter um trabalho, publicar o ativo de saída para o streaming HLS e DASH.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Orientação e boas práticas para um sistema de produção que utilize codificação ou análise a pedido. Os leitores devem começar com o artigo [de acompanhante Alta Disponibilidade com Serviços de Mídia e VOD](https://docs.microsoft.com/azure/media-services/latest/media-services-high-availability-encoding). Existe um ficheiro de solução separado fornecido para a amostra [HighAvailabilityEncodingStreaming.](/HighAvailabilityEncodingStreaming/Readme.md) |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Pasta|Description|
|---|---|
|[HelloWorld-ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |Como conectar e listar ativos |
|[Em direto](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| Como fazer transmissão básica ao vivo. **ATENÇÃO**, certifique-se de que todos os recursos estão limpos e que já não fazem faturação no portal durante a utilização ao vivo.|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| Como carregar um ficheiro local ou codificar a partir de um URL de origem, como usar o armazenamento SDK para descarregar conteúdo e como transmitir para um jogador. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Como codificar e transmitir usando Widevine e PlayReady DRM |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| Como utilizar as predefinições do Video and Audio Analyzer para gerar metadados e insights a partir de um ficheiro de vídeo ou áudio. |

## <a name="python"></a>[Python](#tab/python)

Atualmente, existe uma amostra python, [codificação básica com Python.](https://github.com/Azure-Samples/media-services-v3-python)

## <a name="java"></a>[Java](#tab/java)

|Pasta|Description|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Como analisar áudio num ficheiro de mídia. |
|Proteção de Conteúdo|
|Proteção de Conteúdos||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|Como encriptar dinamicamente o seu conteúdo com a AES-128.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Como encriptar dinamicamente o seu conteúdo com o PlayReady DRM.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Como encriptar dinamicamente o seu conteúdo com a Widevine DRM.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|Como encriptar dinamicamente o seu conteúdo com FairPlay DRM e reproduzir o conteúdo sem solicitar uma licença do serviço de licença.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|Como encriptar dinamicamente o seu conteúdo com a PlayReady e a Widevine DRM e reproduzir o conteúdo sem solicitar uma licença do serviço de licença.|
|DynamicPackagingVODContent||
|[Filtros de Ativos](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Como filtrar o conteúdo utilizando filtros de ativos e de conta.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Como embalar dinamicamente o conteúdo do VOD em HLS/DASH para streaming.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Como criar um Evento Ao Vivo com um arquivo completo até 25 horas e um filtro no ativo com janela de 5 minutos de DVR e como criar um localizador para streaming e usar o filtro.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Como criar um Evento Ao Vivo com um arquivo completo até 25 horas e um filtro no ativo com janela de 5 minutos de DVR e como criar um localizador para streaming e usar o filtro.|
|VideoEncoding||
|[Codificação ComMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Como criar uma transformação de codificação personalizada utilizando as definições StandardEncoderPreset.|
|[Codificação ComMESPredefinedPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Como submeter um trabalho usando uma predefinição incorporada e uma entrada HTTP URL, publicar o ativo de saída para streaming e baixar os resultados para verificação.|

---
