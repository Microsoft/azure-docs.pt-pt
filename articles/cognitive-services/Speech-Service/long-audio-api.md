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
ms.openlocfilehash: f03e32eb17c7c02d8969ea6f79663a5c99196d7b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931938"
---
# <a name="long-audio-api-preview"></a>API de áudio longo (visualização)

A API de áudio longo é projetada para a síntese assíncrona de texto em fala de longa forma (por exemplo: livros de áudio). Essa API não retorna áudio sintetizado em tempo real, em vez disso, a expectativa é que você pesquise as respostas e consuma as saídas conforme elas são disponibilizadas pelo serviço. Ao contrário da API de texto para fala usada pelo SDK de fala, a API de áudio de longa duração pode criar áudio sintetizado por mais de 10 minutos, tornando-o ideal para Publicadores e plataformas de conteúdo de áudio.

Benefícios adicionais da API de áudio longa:

* A fala sintetizada retornada pelo serviço usa vozes neurais, o que garante saídas de áudio de alta fidelidade.
* Como não há suporte para respostas em tempo real, não há necessidade de implantar um ponto de extremidade de voz.

> [!NOTE]
> A API de áudio longo agora dá suporte apenas à [voz neural personalizada](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Fluxo de trabalho

Normalmente, ao usar a API de áudio longo, você enviará um arquivo de texto ou arquivos a serem sintetizados, sondando o status e, se o status for bem-sucedido, você poderá baixar a saída de áudio.

Este diagrama fornece uma visão geral de alto nível do fluxo de trabalho.

![Diagrama de fluxo de trabalho da API de áudio longo](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparar conteúdo para síntese

Ao preparar seu arquivo de texto, verifique se:

* É texto sem formatação (. txt) ou o texto SSML (. txt)
  * Para texto sem formatação, cada parágrafo é separado ao pressionar o exemplo **Inserir/retornar** -exibir [entrada de texto sem formatação](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Para o texto SSML, cada parte da SSML é considerada um parágrafo. As partes de SSML devem ser separadas por parágrafos diferentes-exibir [exemplo de entrada de texto de SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt). Para ver o código de idioma, consulte [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md)
* É codificado como [UTF-8 com marca de ordem de byte (bom)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Contém mais de 10.000 caracteres ou mais de 50 parágrafos
* É um arquivo único, não um zip

## <a name="audio-output-formats"></a>Formatos de saída de áudio

Os seguintes formatos de saída de áudio são suportados pela API de áudio longa:

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

## <a name="quickstarts"></a>Inícios rápidos

Oferecemos guias de início rápido projetados para ajudá-lo a executar a API de áudio longo com êxito. Esta tabela inclui uma lista de guias de início rápido da API de áudio longa organizados por idioma.

* [Início rápido: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Código de exemplo
O código de exemplo para a API de áudio de longa disponibilidade está disponível no GitHub.

* [Código de exemplo: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Código de exemplo:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Código de exemplo: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
