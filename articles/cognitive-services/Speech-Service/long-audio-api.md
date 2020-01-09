---
title: API de áudio longo (visualização)-serviço de fala
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: ff8cdf78d923394caf36610534eb5dcc7de571a4
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562549"
---
# <a name="long-audio-api-preview"></a>API de áudio longo (visualização)

A API de áudio longo é projetada para a síntese assíncrona de texto em fala de longa forma (por exemplo: livros de áudio). Essa API não retorna áudio sintetizado em tempo real, em vez disso, a expectativa é que você pesquise as respostas e consuma as saídas conforme elas são disponibilizadas pelo serviço. Ao contrário da API de texto para fala usada pelo SDK de fala, a API de áudio de longa duração pode criar áudio sintetizado por mais de 10 minutos, tornando-o ideal para Publicadores e plataformas de conteúdo de áudio.

Benefícios adicionais da API de áudio longa:

* A fala sintetizada retornada pelo serviço usa vozes neurais, o que garante saídas de áudio de alta fidelidade.
* Como não há suporte para respostas em tempo real, não há necessidade de implantar um ponto de extremidade de voz.

> [!NOTE]
> A API de áudio longo agora dá suporte apenas à [voz neural personalizada](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Fluxo de Trabalho

Normalmente, ao usar a API de áudio longo, você enviará um arquivo de texto ou arquivos a serem sintetizados, sondando o status e, se o status for bem-sucedido, você poderá baixar a saída de áudio.

Este diagrama fornece uma visão geral de alto nível do fluxo de trabalho.

![Diagrama de fluxo de trabalho da API de áudio longo](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparar conteúdo para síntese

Ao preparar seu arquivo de texto, verifique se:

* É texto sem formatação (. txt) ou o texto SSML (. txt)
* É codificado como [UTF-8 com marca de ordem de byte (bom)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* É um arquivo único, não um zip
* Contém mais de 400 caracteres para texto sem formatação ou 400 [caracteres cobráveis](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) para texto de SSML e menos de 10.000 parágrafos
  * Para texto sem formatação, cada parágrafo é separado ao pressionar o exemplo **Inserir/retornar** -exibir [entrada de texto sem formatação](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Para o texto SSML, cada parte da SSML é considerada um parágrafo. As partes de SSML devem ser separadas por parágrafos diferentes – exibir [exemplo de entrada de texto de SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Para chinês (continente), chinês (Hong Kong), chinês (Taiwan), japonês e coreano, uma palavra será contada como dois caracteres. 

## <a name="submit-synthesis-requests"></a>Enviar solicitações de síntese

Depois de preparar o conteúdo de entrada, siga o [início rápido de síntese de áudio de forma longa](https://aka.ms/long-audio-python) para enviar a solicitação. Se você tiver mais de um arquivo de entrada, será necessário enviar várias solicitações. Há algumas limitações a serem consideradas: 
* O cliente tem permissão para enviar até 5 solicitações ao servidor por segundo para cada conta de assinatura do Azure. Se ele exceder a limitação, o cliente receberá um código de erro 429 (muitas solicitações). Reduza o valor da solicitação por segundo
* O servidor tem permissão para executar e enfileirar até 120 solicitações para cada conta de assinatura do Azure. Se ele exceder a limitação, o servidor retornará um código de erro 429 (número excessivo de solicitações). Aguarde e evite enviar uma nova solicitação até que algumas solicitações sejam concluídas
* O servidor manterá até 20.000 solicitações para cada conta de assinatura do Azure. Se ele exceder a limitação, exclua algumas solicitações antes de enviar novas.

## <a name="audio-output-formats"></a>Formatos de saída de áudio

Damos suporte a formatos de saída de áudio flexíveis. Você pode gerar saídas de áudio por parágrafo ou concatenar os áudios em uma saída definindo o parâmetro ' concatenateResult '. Os seguintes formatos de saída de áudio são suportados pela API de áudio longa:

> [!NOTE]
> O formato de áudio padrão é riff-16kHz-16 bits-mono-PCM.

* riff-8kHz-16 bits-mono-PCM
* riff-16kHz-16 bits-mono-PCM
* riff-24kHz-16 bits-mono-PCM
* riff-48kHz-16 bits-mono-PCM
* Audio-16kHz-32kbitrate-mono-mp3
* Audio-16kHz-64kbitrate-mono-mp3
* Audio-16kHz-128kbitrate-mono-mp3
* Audio-24kHz-48kbitrate-mono-mp3
* Audio-24kHz-96kbitrate-mono-mp3
* Audio-24kHz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Guias de Início Rápido

Oferecemos guias de início rápido projetados para ajudá-lo a executar a API de áudio longo com êxito. Esta tabela inclui uma lista de guias de início rápido da API de áudio longa organizados por idioma.

* [Início rápido: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Código de exemplo
O código de exemplo para a API de áudio de longa disponibilidade está disponível no GitHub.

* [Código de exemplo: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Código de exemplo:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Código de exemplo: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
