---
title: Cenários de preços para chamada (Voz/Vídeo) e Chat
titleSuffix: An Azure Communication Services concept document
description: Conheça o Modelo de Preços dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 94a34937af92eefa6146c7a043b6d5d68cf6b852
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947424"
---
# <a name="pricing-scenarios"></a>Cenários de preços

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Os preços dos Serviços de Comunicação Azure baseiam-se num modelo pay-as-you-go sem taxas antecipadas. Só será cobrado pelo seu consumo e uso dos serviços.

## <a name="voicevideo-calling-and-screen-sharing"></a>Chamada de voz/vídeo e partilha de ecrã

Os Serviços de Comunicação Azure permitem adicionar chamadas de voz/vídeo e partilha de ecrãs às suas aplicações. Pode incorporar a experiência nas suas aplicações utilizando bibliotecas de clientes JavaScript, Objective-C (Apple), Java (Android) ou .NET. Consulte a nossa [lista completa de bibliotecas de clientes disponíveis.](./sdk-options.md)

### <a name="pricing"></a>Preços

Os serviços de chamada e partilha de ecrãs são cobrados por minuto por minuto por participante a $0,004 por participante por minuto para chamadas de grupo. Para compreender os vários fluxos de chamadas que são possíveis, consulte [esta página](./call-flows.md).

Cada participante da chamada contará na faturação por cada minuto que estiver ligado à chamada. Isto é verdade independentemente de o utilizador estar a chamar por vídeo, a chamar voz ou a partilhar ecrãs.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Exemplo de preços: Chamada de áudio/vídeo em grupo utilizando bibliotecas de clientes JS e iOS

A Alice fez uma chamada em grupo com os colegas, Bob e Charlie. Alice e Bob usaram as bibliotecas de clientes da JS, bibliotecas de clientes Charlie iOS.

- A chamada dura um total de 60 minutos.
- Alice e Bob participaram durante toda a ligação. Alice ligou o vídeo durante cinco minutos e partilhou o ecrã durante 23 minutos. Bob tinha o vídeo ligado durante toda a chamada (60 minutos) e partilhou o ecrã durante 12 minutos.
- Charlie deixou a ligação após 43 minutos. Charlie usou áudio e vídeo durante o tempo em que participou (43 minutos).

**Cálculos de custos**

- 2 participantes x 60 minutos x $0,004 por participante por minuto = $0,48 [tanto o vídeo como o áudio são cobrados à mesma taxa]
- 1 participante x 43 minutos x $0,004 por participante por minuto = $0,172 [tanto o vídeo como o áudio são cobrados à mesma taxa]

**Custo total da chamada do grupo**: $0,48 + $0,172 = $0,652

## <a name="chat"></a>Chat

Com os Serviços de Comunicação pode melhorar a sua aplicação com a capacidade de enviar e receber mensagens de chat entre 2 ou mais utilizadores. As bibliotecas de clientes chat estão disponíveis para JavaScript, .NET, Python e Java. Consulte [esta página para saber mais sobre bibliotecas de clientes](./sdk-options.md)

### <a name="price"></a>Preço

- São cobrados $0.0008 por cada mensagem de chat enviada.

### <a name="pricing-example-chat-between-two-users"></a>Exemplo de preços: Conversa entre dois utilizadores 

Geeta inicia um chat thread com Emily para partilhar uma atualização e envia 5 mensagens. A conversa dura 10 minutos onde Geeta e Emily enviam mais 15 mensagens cada.

**Cálculos de custos** 
- Número de mensagens enviadas (5 + 15 + 15) x $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Exemplo de preços: Chat de grupo com vários utilizadores 

Charlie começa uma conversa com os amigos Casey & Jasmine para planear umas férias. Conversam durante algum tempo onde Charlie, Casey & Jasmine enviam 20, 30 e 18 mensagens, respectivamente. Eles percebem que a sua amiga Rose também pode estar interessada em juntar-se à viagem, por isso acrescentam-na ao chat thread e partilham toda a história da mensagem com ela. 

Rose vê as mensagens e começa a conversar. Entretanto, o Casey recebe uma chamada e decide pôr a conversa em dia mais tarde. Charlie, Jasmine & Rose decidem as datas de viagem e enviam mais 30, 25, 35 mensagens, respectivamente.

**Cálculos de custos** 

- Número de mensagens enviadas (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264
