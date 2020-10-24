---
title: Transcrição de Conversação (Pré-visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A Transcrição de Conversação é uma solução para reuniões, que combina reconhecimento, identificação de altifalantes e diarização para fornecer transcrição de qualquer conversa.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: 5d95525c7dfecd44758c86903ca4a96c2290df52
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487789"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>O que é Transcrição de Conversa nas reuniões (Pré-visualização)?

A Transcrição [de](speech-to-text.md) Conversação é uma solução de discurso a texto que combina reconhecimento de voz, identificação de altifalantes e atribuição de frases a cada orador (também conhecido como _diarização)_ para fornecer transcrição em tempo real e/ou assíncronia de qualquer conversa. A Transcrição de Conversação distingue os oradores numa conversa para determinar quem disse o quê e quando, e facilita que os desenvolvedores adicionem discurso a texto às suas aplicações que realizam a diarização de vários altifalantes.

## <a name="key-features"></a>Principais funcionalidades

- **Timetamps** - cada expressão de orador tem uma hora de tempo, para que você possa facilmente encontrar quando uma frase foi dita.
- **Transcrições legíveis** - as transcrições têm formatação e pontuação adicionadas automaticamente para garantir que o texto corresponde de perto ao que estava a ser dito.
- **Perfis do utilizador** - os perfis do utilizador são gerados recolhendo amostras de voz do utilizador e enviando-as para a geração de assinaturas.
- **Identificação do altifalante** - os altifalantes são identificados utilizando perfis de utilizador e um _identificador de altifalantes_ é atribuído a cada um.
- **Diarização multi-altifalante** - determine quem disse o quê sintetizando o fluxo de áudio com cada identificador de altifalantes.
- **Transcrição em tempo** real – forneça transcrições ao vivo de quem está a dizer o quê e quando a conversa está a acontecer.
- **transcrição assíncronia** – forneça transcrições com maior precisão utilizando um fluxo de áudio multicanal.

> [!NOTE]
> Embora a Transcrição de Conversação não limite o número de altifalantes na sala, está otimizada para 2-10 altifalantes por sessão.

## <a name="get-started"></a>Introdução

Veja o [início da](how-to-use-conversation-transcription.md) transcrição da conversa em tempo real para começar.

## <a name="use-cases"></a>Casos de utilização

Para tornar as reuniões inclusivas para todos, como os participantes surdos e com dificuldade de audição, é importante ter transcrição em tempo real. A transcrição da conversa em tempo real requer o áudio do encontro e determina quem está a dizer o quê, permitindo que todos os participantes do encontro sigam a transcrição e participem na reunião sem demora.

### <a name="improved-efficiency"></a>Melhoria da eficiência

Os participantes do encontro podem focar-se na reunião e deixar notas para a Transcrição de Conversação. Os participantes podem participar ativamente na reunião e acompanhar rapidamente os próximos passos, usando a transcrição em vez de tomar notas e potencialmente perder algo durante a reunião.

## <a name="how-it-works"></a>Como funciona

Esta é uma visão geral de alto nível de como a Transcrição de Conversa funciona.

![O diagrama de transcrição da conversa de importação](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Entradas esperadas

- **Fluxo de áudio multicanal** - Para obter detalhes de especificação e design, consulte [o microfone SDK do Dispositivo de Fala do Microsoft](https://aka.ms/cts/microphone). Para saber mais ou comprar um kit de desenvolvimento, consulte [Get Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).
- **Amostras de voz do utilizador** – A Transcrição de Conversação necessita de perfis de utilizador antes da conversação. Terá de recolher gravações áudio de cada utilizador e, em seguida, enviar as gravações para o [Serviço de Geração de Assinaturas](https://aka.ms/cts/signaturegenservice) para validar o áudio e gerar perfis de utilizador.

> [!NOTE]
> As amostras de voz do utilizador são opcionais. Sem esta entrada, a transcrição apresentará diferentes altifalantes, mas mostrados como "Speaker1", "Speaker2", etc. em vez de reconhecer como nomes de oradores específicos pré-inscritos.


## <a name="real-time-vs-asynchronous"></a>Em tempo real vs. assíncronos

A Transcrição de Conversação oferece três modos de transcrição:

### <a name="real-time"></a>Em tempo real

Os dados áudio são processados em direto para devolver o identificador de altifalante + transcrição. Selecione este modo se o requisito da sua solução de transcrição for fornecer aos participantes da conversa uma visão ao vivo da sua conversa contínua. Por exemplo, a construção de uma aplicação para tornar as reuniões mais acessíveis aos surdos e aos participantes de audição é um caso de uso ideal para a transcrição em tempo real.

### <a name="asynchronous"></a>Assíncrono

Os dados áudio são processados para devolver o identificador de altifalantes e a transcrição. Selecione este modo se o seu requisito de solução de transcrição for fornecer maior precisão sem visualização da transcrição ao vivo. Por exemplo, se quiser construir uma aplicação que permita aos participantes da reunião acompanhar facilmente as reuniões perdidas, use o modo de transcrição assíncronos para obter resultados de transcrição de alta precisão.

### <a name="real-time-plus-asynchronous"></a>Em tempo real mais assíncronos

Os dados áudio são processados ao vivo para devolver o identificador de altifalante + transcrição, e, além disso, é criado um pedido para obter também uma transcrição de alta precisão através do processamento assíncrona. Selecione este modo se a sua aplicação tiver necessidade de transcrição em tempo real, mas também requer uma transcrição de maior precisão para ser usada após a conversação ou reunião ocorrer.

## <a name="language-support"></a>Suporte de idiomas

Atualmente, a Transcrição de Conversação apoia [todas as línguas de expressão para texto](language-support.md#speech-to-text) nas seguintes regiões: . . . . .  `centralus` `eastasia` `eastus` `westeurope` . Se necessitar de apoio local adicional, contacte a Equipa de [Recurso de Transcrição de Conversação](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transcreva conversas em tempo real](how-to-use-conversation-transcription.md)
