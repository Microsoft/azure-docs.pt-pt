---
title: API de áudio longo (pré-visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como a API de Áudio Longo é projetada para uma síntese assíncrona de texto de longa forma para a fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401064"
---
# <a name="long-audio-api-preview"></a>API de áudio longo (pré-visualização)

A API de Áudio Longo foi concebida para uma síntese assíncrona de texto de longa forma para a fala (por exemplo: livros áudio). Esta API não devolve áudio sintetizado em tempo real, em vez disso a expectativa é que você faça uma sondagem para a(s) resposta(s) e consuma a(s) saída(s) à medida que são disponibilizados a partir do serviço. Ao contrário do texto para a fala API que é usado pelo Speech SDK, a API de Áudio Longo pode criar áudio sintetizado mais de 10 minutos, tornando-o ideal para editores e plataformas de conteúdo sonoro.

Benefícios adicionais da API de Áudio Longo:

* O discurso sintetizado devolvido pelo serviço usa vozes neurais, o que garante saídas de áudio de alta fidelidade.
* Como as respostas em tempo real não são suportadas, não há necessidade de implantar um ponto final de voz.

> [!NOTE]
> A API de áudio longo suporta agora apenas [voz neural personalizada](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Fluxo de trabalho

Normalmente, ao utilizar a API de Áudio Longo, submeterá um ficheiro de texto ou ficheiros para ser sintetizado, pesquisa para o estado, então se o estado for bem sucedido, pode descarregar a saída de áudio.

Este diagrama fornece uma visão geral de alto nível do fluxo de trabalho.

![Diagrama de fluxo de trabalho de API de áudio longo](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparar conteúdo para a síntese

Ao preparar o seu ficheiro de texto, certifique-se de que:

* É texto simples (.txt) ou texto SSML (.txt)
* É codificado como [UTF-8 com Byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* É um único ficheiro, não um fecho.
* Contém mais de 400 caracteres para texto simples ou 400 [caracteres de billable](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) para texto SSML, e menos de 10.000 parágrafos
  * Para texto simples, cada parágrafo é separado ao bater **Enter/Return** - Ver exemplo de entrada de [texto simples](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Para texto SSML, cada peça SSML é considerada um parágrafo. As peças SSML devem ser separadas por diferentes parágrafos - Ver exemplo de entrada de [texto SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Para os chineses (continente), chinês (Hong Kong), chinês (Taiwan), japonês e coreano, uma palavra será contada como dois caracteres. 

## <a name="submit-synthesis-requests"></a>Enviar pedidos de síntese

Depois de preparar o conteúdo de entrada, siga [rapidamente](https://aka.ms/long-audio-python) a síntese áudio de forma longa para submeter o pedido. Se tiver mais de um ficheiro de entrada, terá de submeter vários pedidos. Existem algumas limitações a ter em conta: 
* O cliente está autorizado a submeter até 5 pedidos ao servidor por segundo para cada conta de subscrição do Azure. Se exceder a limitação, o cliente receberá um código de erro 429 (pedidos a mais). Por favor, reduza o valor do pedido por segundo
* O servidor está autorizado a executar e fazer fila até 120 pedidos para cada conta de subscrição do Azure. Se exceder a limitação, o servidor devolverá um código de erro 429 (pedidos a mais). Por favor, aguarde e evite submeter novo pedido até que alguns pedidos estejam concluídos
* O servidor manterá até 20.000 pedidos para cada conta de subscrição do Azure. Se exceder a limitação, por favor apague alguns pedidos antes de enviar novos

## <a name="audio-output-formats"></a>Formatos de saída de áudio

Apoiamos formatos flexíveis de saída de áudio. Pode gerar saídas de áudio por parágrafo ou concatenar os áudios numa saída, definindo o parâmetro 'concatenaresult'. Os seguintes formatos de saída de áudio são suportados pela API de Áudio Longo:

> [!NOTE]
> O formato de áudio padrão é riff-16khz-16bit-mono-pcm.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* áudio-16khz-32kbitrate-mono-mp3
* áudio-16khz-64kbitrate-mono-mp3
* áudio-16khz-128kbitrate-mono-mp3
* áudio-24khz-48kbitrate-mono-mp3
* áudio-24khz-96kbitrate-mono-mp3
* áudio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Guias de Início Rápido

Oferecemos quickstarts projetados para ajudá-lo a executar a API de Áudio Longo com sucesso. Esta tabela inclui uma lista de quickstarts DaAPi de Áudio Longo organizados por linguagem.

* [Quickstart: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Código de exemplo
O código de amostra para API de Áudio Longo está disponível no GitHub.

* [Código da amostra: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Código da amostra: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Código da amostra: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
