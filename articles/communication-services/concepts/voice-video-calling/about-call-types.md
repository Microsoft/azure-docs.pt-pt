---
title: Conceitos de voz e vídeo nos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os tipos de chamadas dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 97a90284ad8d195751eb5dd90675822d00243ea8
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665213"
---
# <a name="voice-and-video-concepts"></a>Conceitos de voz e vídeo

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Pode utilizar os Serviços de Comunicação Azure para fazer e receber chamadas de voz e vídeo, analisar metadados de chamadas, gravar chamadas e até construir call trees. As suas chamadas podem ser feitas para outros dispositivos ligados à Internet e para telefones simples. Pode utilizar as bibliotecas de clientes JavaScript, Android ou iOS para construir aplicações que permitam aos seus utilizadores falar em conversas privadas ou em discussões de grupo. A Azure Communication Services suporta chamadas de e para serviços ou Bots.

## <a name="call-types-in-azure-communication-services"></a>Tipos de chamadas nos Serviços de Comunicação Azure

Existem vários tipos de chamadas que pode fazer nos Serviços de Comunicação Azure. O tipo de chamadas que faz determinam o seu esquema de sinalização, fluxos de tráfego de mídia e modelo de preços.

### <a name="voice-over-ip-voip"></a>Voz sobre IP (VoIP) 

Quando um utilizador da sua aplicação chama outro utilizador da sua aplicação através de uma ligação à Internet ou aos dados, a chamada é feita através do Voice Over IP (VoIP). Neste caso, tanto a sinalização como os meios de comunicação fluem pela internet.

### <a name="public-switched-telephone-network-pstn"></a>Rede telefónica comutado (PSTN)

Sempre que os seus utilizadores interagem com um número de telefone tradicional, as chamadas são facilitadas pela chamada de voz PSTN (Public Switched Telephone Network). Para escruquisar e receber chamadas PSTN, precisa adicionar capacidades de telefonia ao seu recurso Azure Communication Services. Neste caso, a sinalização e os meios de comunicação utilizam uma combinação de tecnologias baseadas em IP e PSTN para ligar os seus utilizadores.

### <a name="one-to-one-call"></a>Uma chamada de um para um

Uma chamada de um para um nos Serviços de Comunicação Azure acontece quando um dos seus utilizadores se conecta a outro utilizador usando uma das bibliotecas dos nossos clientes. A chamada pode ser VoIP ou PSTN.

### <a name="group-call"></a>Chamada de grupo

Uma chamada de grupo nos Serviços de Comunicação Azure acontece quando três ou mais participantes se ligam entre si. Qualquer combinação de utilizadores ligados a VoIP e PSTN pode estar presente numa chamada de grupo. Uma chamada de um para um pode ser convertida numa chamada de grupo adicionando mais participantes à chamada. Um desses participantes pode ser um bot.

### <a name="supported-video-standards"></a>Padrões de vídeo suportados
Apoiamos H.264 (MPEG-4) 

### <a name="video-quality"></a>Qualidade do vídeo 
Apoiamos até Full HD 1080p nos SDKs nativos (iOS, Android). Para Web (JS) SDK apoiamos Standard HD 720p. A qualidade depende da largura de banda disponível.  

### <a name="rooms-concept"></a>Conceito de quartos
Os quartos são um conjunto de APIs e SDKs que permitem adicionar facilmente interações de áudio, vídeo, partilha de ecrã, PSTN e SMS ao seu website ou aplicação nativa.
Durante a pré-visualização pode utilizar o ID do grupo para se juntar à mesma conversa. Pode criar o iD de grupo que precisar e separar os utilizadores pelos "quartos". Avançar vai introduzir mais controlos em torno de "quartos" 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Começa com a chamada](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Para obter mais informações, veja os seguintes artigos:
- Familiarize-se com fluxos de [chamadas gerais](../call-flows.md)
- [Planeie a sua solução PSTN](../telephony-sms/plan-solution.md)
- Saiba mais sobre as [capacidades da biblioteca do cliente chamado](../voice-video-calling/calling-sdk-features.md)
