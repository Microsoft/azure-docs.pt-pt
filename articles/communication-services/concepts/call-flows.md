---
title: Fluxos de chamadas nos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre fluxos de chamadas nos Serviços de Comunicação Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5b1d24dc6056de0b8dd19d0d0e52c85055596a1d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664131"
---
# <a name="call-flow-basics"></a>Básicos de fluxo de chamada

A secção abaixo apresenta uma visão geral dos fluxos de chamadas nos Serviços de Comunicação Azure. A sinalização e os fluxos de mídia dependem do tipo de chamadas que os seus utilizadores estão a fazer. Exemplos de tipos de chamadas incluem VoIP de um para um, PSTN um a um, e chamadas de grupo contendo uma combinação de participantes ligados ao VoIP e PSTN. Rever [tipos de chamadas](./voice-video-calling/about-call-types.md).

## <a name="about-signaling-and-media-protocols"></a>Sobre protocolos de sinalização e media

Quando estabelece uma chamada peer-to-peer ou grupo, são utilizados dois protocolos nos bastidores - HTTP (REST) para sinalização e SRTP para meios de comunicação.

A sinalização entre as bibliotecas do cliente ou entre bibliotecas de clientes e controladores de sinalização de serviços de comunicação é tratada com HTTP REST (TLS). Para Real-Time Tráfego de Meios de Comunicação Social (RTP), é preferível o Protocolo de Datagrama do Utilizador (UDP). Se a utilização de UDP for impedida pela sua firewall, a biblioteca do cliente utilizará o Protocolo de Controlo de Transmissão (TCP) para meios de comunicação.

Vamos rever os protocolos de sinalização e media em vários cenários.

## <a name="call-flow-cases"></a>Casos de fluxo de chamadas

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>Caso 1: VoIP onde é possível uma ligação direta entre dois dispositivos

Em chamadas voIP ou vídeo-1, o tráfego prefere o caminho mais direto. "Caminho direto" significa que se duas bibliotecas de clientes se alcançarem diretamente, estabelecerão uma ligação direta. Isto é geralmente possível quando duas bibliotecas clientes estão na mesma sub-rede (por exemplo, numa sub-rede 192.168.1.0/24) ou duas quando os dispositivos que cada um vive em sub-redes que podem ver-se mutuamente (bibliotecas de clientes na sub-rede 10.10.0/16 e 192.168.1.0/24 podem alcançar-se mutuamente).

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="Diagrama mostrando uma chamada VOIP direta entre utilizadores e Serviços de Comunicação.":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>Caso 2: VoIP onde não é possível uma ligação direta entre dispositivos, mas onde é possível a ligação entre dispositivos NAT

Se dois dispositivos estiverem localizados em sub-redes que não conseguem chegar uns aos outros (por exemplo, Alice trabalha a partir de um café e Bob trabalha a partir do seu escritório) mas a ligação entre os dispositivos NAT é possível, as bibliotecas do cliente do cliente estabelecerão conectividade através de dispositivos NAT.

Para Alice será o NAT do café e para Bob será o NAT do escritório. O dispositivo da Alice vai enviar o endereço externo da NAT e o Bob vai fazer o mesmo. As bibliotecas de clientes aprendem os endereços externos a partir de um serviço STUN (Session Traversal Utilities for NAT) que a Azure Communication Services fornece gratuitamente. A lógica que lida com o aperto de mão entre Alice e Bob está incorporada nos Serviços de Comunicação Azure fornecidos bibliotecas de clientes. (Não precisa de nenhuma configuração adicional)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="Diagrama mostrando uma chamada VOIP que utiliza uma ligação STUN.":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>Caso 3: VoIP onde nem uma ligação direta nem NAT é possível

Se um ou ambos os dispositivos clientes estiverem por detrás de um NAT simétrico, é necessário um serviço de nuvem separado para transmitir os meios de comunicação entre as duas bibliotecas clientes. Este serviço chama-se TURN (Traversal Using Relays em torno do NAT) e também é fornecido pelos Serviços de Comunicação. Os Serviços de Comunicação que ligam para a biblioteca de clientes utilizam automaticamente os serviços TURN com base nas condições de rede detetadas. A utilização do serviço TURN da Microsoft é carregada separadamente.

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="Diagrama mostrando uma chamada VOIP que utiliza uma ligação TURN.":::

### <a name="case-4-group-calls-with-pstn"></a>Caso 4: Chamadas de grupo com PSTN

Tanto a sinalização como os meios de comunicação para chamadas PSTN utilizam o recurso de telefonia dos Serviços de Comunicação Azure. Este recurso está interligado com outros transportadores.

O tráfego de mídia PSTN flui através de um componente chamado Processador de Mídia.

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="Diagrama mostrando uma chamada de grupo PSTN com serviços de comunicação.":::

> [!NOTE]
> Para aqueles que estão familiarizados com o processamento de mídia, o nosso Processador de Media é também um Agente de Utilizador Back to Back, tal como definido no [RFC 3261 SIP: Session Initiation Protocol](https://tools.ietf.org/html/rfc3261), o que significa que pode traduzir codecs ao lidar com chamadas entre as redes Microsoft e Carrier. O Controlador de Sinalização de Serviços de Comunicação Azure é a implementação da Microsoft de um Proxy SIP pelo mesmo RFC.

Para chamadas de grupo, os meios de comunicação e a sinalização fluem sempre através do backend dos Serviços de Comunicação Azure. O áudio e/ou vídeo de todos os participantes é misturado na componente do Processador de Mídia. Todos os membros de uma chamada de grupo enviam os seus streams de áudio e/ou vídeo para o processador de mídia, que devolve streams mistos de mídia.

O protocolo em tempo real (RTP) para chamadas de grupo é o Protocolo de Datagrama do Utilizador (UDP).

> [!NOTE]
> O processador de mídia pode funcionar como uma Unidade de Controlo de Vários Pontos (MCU) ou Unidade de Reencaminhamento Seletivo (SFU)

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="Diagrama mostrando o fluxo de processo de mídia UDP nos Serviços de Comunicação.":::

Se a biblioteca do cliente não puder utilizar o UDP para meios de comunicação devido a restrições de firewall, será feita uma tentativa de utilização do Protocolo de Controlo de Transmissão (TCP). Note que o componente do processador de mídia requer UDP, pelo que, quando isso acontecer, o serviço DE TURN dos Serviços de Comunicação será adicionado à chamada de grupo para traduzir tCP para UDP. As cargas TURN serão incorridos neste caso, a menos que as capacidades TURN sejam desativadas manualmente.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="Diagrama mostrando o fluxo de processo de mídia TCP nos Serviços de Comunicação.":::

### <a name="case-5-communication-services-client-library-and-microsoft-teams-in-a-scheduled-teams-meeting"></a>Caso 5: Biblioteca de clientes de serviços de comunicação e equipas da Microsoft numa reunião agendada de equipas

A sinalização flui através do controlador de sinalização. Os meios de comunicação fluem através do Processador de Mídia. O controlador de sinalização e o processador de mídia são partilhados entre os Serviços de Comunicação e as Equipas microsoft.

:::image type="content" source="./media/call-flows/teams-communication-services-meeting.png" alt-text="Diagrama mostrando biblioteca de clientes de Serviços de Comunicação e Cliente de Equipas numa reunião agendada para equipas.":::



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começa com a chamada](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Os seguintes documentos podem ser interessantes para si:

- Saiba mais sobre [tipos de chamadas](../concepts/voice-video-calling/about-call-types.md)
- Conheça a [arquitetura do servidor de clientes](./client-and-server-architecture.md)
- Saiba mais sobre [as topologias do fluxo de chamadas](./detailed-call-flows.md)
