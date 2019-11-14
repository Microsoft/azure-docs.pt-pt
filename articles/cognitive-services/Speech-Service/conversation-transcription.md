---
title: O que é a transcrição de conversa (versão prévia) – serviço de fala
titleSuffix: Azure Cognitive Services
description: A transcrição de conversa é uma solução de fala para texto que combina reconhecimento de fala, identificação do orador e atribuição de frase a cada palestrante (também conhecido como diarization) para fornecer transcrição em tempo real e/ou assíncrona de qualquer escreve.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 0c15b053028bd707159a632c98faaea8b9954a9b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075855"
---
# <a name="what-is-conversation-transcription-preview"></a>O que é a transcrição de conversa (versão prévia)?

A transcrição de conversa é uma solução de [fala para texto](speech-to-text.md) que combina reconhecimento de fala, identificação do orador e atribuição de frase a cada palestrante (também conhecido como _diarization_) para fornecer tempo real e/ou assíncrono transcrição de qualquer conversa. A transcrição de conversa distingue os alto-falantes em uma conversa para determinar quem disse o que e quando, e torna mais fácil para os desenvolvedores adicionar a conversão de texto em seus aplicativos que executam diarization de vários palestrantes.

## <a name="key-features"></a>Principais funcionalidades

- **Carimbos** de data/hora-cada expressão do palestrante tem um carimbo de timestamp, para que você possa encontrar facilmente quando uma frase foi mencionada.
- **Transcrições legíveis** – as transcrições têm formatação e pontuação adicionadas automaticamente para garantir que o texto corresponda de forma semelhante ao que estava sendo dito.
- **Perfis de usuário** – perfis de usuário são gerados pela coleta de exemplos de voz de usuário e pelo envio à geração de assinatura.
- **Identificação do orador** -os alto-falantes são identificados usando perfis de usuário e um _identificador de palestrante_ é atribuído a cada um.
- **Diarization de vários palestrantes** -determine quem disse o que sintetizando o fluxo de áudio com cada identificador de palestrante.
- **Transcrição em tempo real** – fornecer transcrições ao vivo de quem está dizendo o que e quando a conversa está acontecendo.
- **transcrição assíncrona** – forneça transcrições com maior precisão usando um fluxo de áudio de multicanal.

> [!NOTE]
> Embora a transcrição de conversa não coloque um limite no número de palestrantes na sala, ela é otimizada para 2-10 alto-falantes por sessão.

## <a name="use-cases"></a>Casos de utilização

### <a name="inclusive-meetings"></a>Reuniões inclusivas

Para tornar as reuniões inclusivas para todos, como participantes que são surdos e difíceis de ouvir, é importante ter a transcrição em tempo real. A transcrição de conversa no modo em tempo real leva o áudio de reunião e determina quem está dizendo o que, permitindo que todos os participantes da reunião sigam a transcrição e participem da reunião sem atraso.

### <a name="improved-efficiency"></a>Eficiência aprimorada

Os participantes da reunião podem se concentrar na reunião e deixar de fazer anotações para a transcrição da conversa. Os participantes podem envolver ativamente a reunião e acompanhar rapidamente as próximas etapas, usando a transcrição em vez de fazer anotações e possivelmente perder alguma coisa durante a reunião.

## <a name="how-it-works"></a>Como funciona

Esta é uma visão geral de alto nível de como funciona a transcrição de conversa.

![O diagrama importar transcrição de conversa](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Entradas esperadas

- **Fluxo de áudio de vários canais** – para obter detalhes de especificação e design, consulte [microfone do SDK do dispositivo de fala da Microsoft](https://aka.ms/cts/microphone). Para saber mais ou comprar um kit de desenvolvimento, confira [obter o SDK do dispositivo de fala da Microsoft](https://aka.ms/cts/getsdk).
- **Exemplos de voz de usuário** – a transcrição de conversa precisa de perfis de usuário antes da conversa. Você precisará coletar gravações de áudio de cada usuário e, em seguida, enviar as gravações para o [serviço de geração de assinatura](https://aka.ms/cts/signaturegenservice) para validar o áudio e gerar perfis de usuário.

## <a name="real-time-vs-asynchronous"></a>Em tempo real versus assíncrono

A transcrição de conversa oferece três modos de transcrição:

### <a name="real-time"></a>Tempo real

Os dados de áudio são processados ao vivo para retornar o identificador do orador + transcrição. Selecione esse modo se seu requisito de solução de transcrição for fornecer aos participantes da conversa uma exibição dinâmica de sua conversa em andamento. Por exemplo, criar um aplicativo para tornar as reuniões mais acessíveis os participantes surdos e difíceis de ouvir são um caso de uso ideal para a transcrição em tempo real.

### <a name="asynchronous"></a>Manipulador

Os dados de áudio são processados em lote para retornar o identificador do orador e a transcrição. Selecione este modo se seu requisito de solução de transcrição for fornecer maior precisão sem a exibição dinâmica de transcrição. Por exemplo, se você quiser criar um aplicativo para permitir que os participantes da reunião se acompanhem facilmente em reuniões perdidas, use o modo de transcrição assíncrona para obter resultados de transcrição de alta precisão.

### <a name="real-time-plus-asynchronous"></a>Em tempo real e assíncrono

Os dados de áudio são processados ao vivo para retornar o identificador do orador + transcrição e, além disso, uma solicitação é criada para também obter uma transcrição de alta precisão por meio do processamento assíncrono. Selecione esse modo se seu aplicativo tiver uma necessidade de transcrição em tempo real, mas também exigir uma transcrição de precisão maior para uso depois que a conversa ou a reunião tiver ocorrido.

## <a name="language-support"></a>Suporte de idiomas

Atualmente, a transcrição de conversa dá suporte a "en-US" e a "zh-CN" nas seguintes regiões: *centralus* e *eastasia*. Se precisar de suporte de localidade adicional, contate a [equipe de recursos de transcrição de conversa](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transcrever conversas em tempo real](how-to-use-conversation-transcription-service.md)
