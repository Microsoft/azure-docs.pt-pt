---
title: Transcrição de Conversa (Pré-visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A Transcrição de Conversas é uma solução de discurso a texto que combina reconhecimento de voz, identificação de altifalantes e atribuição de frases a cada orador (também conhecido como diarização) para fornecer transcrição em tempo real e/ou assíncrono de qualquer conversa.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 8fc52428795b0dc8123d501dac9605c473f61456
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367827"
---
# <a name="what-is-conversation-transcription-preview"></a>O que é Transcrição de Conversa (Pré-visualização)?

A Transcrição de Conversas é uma solução [de discurso a texto](speech-to-text.md) que combina reconhecimento de voz, identificação de altifalantes e atribuição de frases a cada orador (também conhecido como _diarização)_ para fornecer transcrição em tempo real e/ou assíncrono de qualquer conversa. A Transcrição de Conversas distingue os oradores numa conversa para determinar quem disse o quê e quando, e facilita que os desenvolvedores adicionem o discurso ao texto às suas aplicações que realizam a diarização multi-colunas.

## <a name="key-features"></a>Principais funcionalidades

- **Carimbos** temporais - cada expressão de orador tem um carimbo temporal, para que você possa facilmente encontrar quando uma frase foi dita.
- **Transcrições legíveis** - as transcrições têm formatação e pontuação adicionadas automaticamente para garantir que o texto corresponde de perto ao que estava a ser dito.
- **Perfis** de utilizador - os perfis do utilizador são gerados através da recolha de amostras de voz do utilizador e enviando-as para a geração de assinaturas.
- **Identificação do altifalante** - os altifalantes são identificados utilizando perfis de utilizador e um _identificador_ de altifalante sou atribuído a cada um.
- **Diarização multi-altifalante** - determine quem disse o quê sintetizando o fluxo de áudio com cada identificador de altifalante.
- **Transcrição em tempo real** – forneça transcrições em direto de quem está a dizer o quê e quando a conversa está a acontecer.
- **transcrição assíncrona** – fornecer transcrições com maior precisão utilizando um fluxo de áudio multicanal.

> [!NOTE]
> Embora a Transcrição de Conversas não imponha um limite no número de altifalantes na sala, está otimizada para 2-10 altifalantes por sessão.

## <a name="use-cases"></a>Casos de utilização

### <a name="inclusive-meetings"></a>Reuniões inclusivas

Para tornar as reuniões inclusivas para todos, como os participantes que são surdos e difíceis de ouvir, é importante ter transcrição em tempo real. A Transcrição da Conversação em modo em tempo real requer áudio de encontro e determina quem está a dizer o quê, permitindo que todos os participantes do encontro sigam a transcrição e participem na reunião sem demora.

### <a name="improved-efficiency"></a>Melhor eficiência

Os participantes do encontro podem concentrar-se na reunião e deixar nota-tomando nota para a Transcrição de Conversas. Os participantes podem envolver-se ativamente na reunião e acompanhar rapidamente os próximos passos, utilizando a transcrição em vez de tomar notas e potencialmente faltar algo durante a reunião.

## <a name="how-it-works"></a>Como funciona

Esta é uma visão geral de alto nível de como a Transcrição de Conversação funciona.

![O Diagrama de Transcrição da Conversa de Importação](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Inputs esperados

- **Fluxo de áudio multicanal** – Para obter especificações e detalhes de design, consulte o [microfone SDK do Microsoft Speech Device](https://aka.ms/cts/microphone). Para saber mais ou comprar um kit de desenvolvimento, consulte O Dispositivo de [Discurso da Microsoft SDK](https://aka.ms/cts/getsdk).
- **Amostras de voz** do utilizador – A Transcrição de Conversas necessita de perfis do utilizador antes da conversação. Terá de recolher gravações áudio de cada utilizador e, em seguida, enviar as gravações para o [Signature Generation Service](https://aka.ms/cts/signaturegenservice) para validar o áudio e gerar perfis de utilizador.

## <a name="real-time-vs-asynchronous"></a>Tempo real vs. assíncrono

A Transcrição de Conversas oferece três modos de transcrição:

### <a name="real-time"></a>Tempo real

Os dados áudio são processados ao vivo para devolver o identificador de altifalantes + transcrição. Selecione este modo se o seu requisito de solução de transcrição for fornecer aos participantes da conversação uma visão de transcrição ao vivo da sua conversa ção em curso. Por exemplo, a construção de uma aplicação para tornar as reuniões mais acessíveis aos surdos e aos participantes de audição difícil é um caso de utilização ideal para a transcrição em tempo real.

### <a name="asynchronous"></a>Assíncrono

Os dados de áudio são processados para devolver o identificador e transcrição do altifalante. Selecione este modo se o seu requisito de solução de transcrição for para fornecer maior precisão sem vista de transcrição ao vivo. Por exemplo, se quiser construir uma aplicação para permitir que os participantes do encontro acompanhem facilmente as reuniões perdidas, use o modo de transcrição assíncrono para obter resultados de transcrição de alta precisão.

### <a name="real-time-plus-asynchronous"></a>Tempo real mais assíncrono

Os dados áudio são processados ao vivo para devolver o identificador de altifalantes + transcrição, e, além disso, é criado um pedido para obter também uma transcrição de alta precisão através de um processamento assíncrono. Selecione este modo se a sua aplicação tiver necessidade de transcrição em tempo real, mas também requer uma transcrição de maior precisão para utilização após a conversa ou reunião.

## <a name="language-support"></a>Suporte de idiomas

Atualmente, a Transcription Conversation apoia "en-US" e "zh-CN" nas seguintes regiões: *centrale*  *eastasia*. Se necessitar de apoio local adicional, contacte a [Equipa de Recursos de Transcrição](mailto:CTSFeatureCrew@microsoft.com)de Conversas .

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transcrever conversas em tempo real](how-to-use-conversation-transcription-service.md)
