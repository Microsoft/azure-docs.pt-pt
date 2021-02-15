---
title: Conceitos de integração da telefonia PSTN para serviços de comunicação Azure
description: Saiba como integrar as capacidades de chamada PSTN na sua aplicação Azure Communication Services.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 02/09/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5f7c2b0168c914c836aca7b551a9c22d4718c7b6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100422470"
---
# <a name="telephony-concepts"></a>Conceitos de telefonia

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Serviços de Comunicação Azure As bibliotecas de clientes podem ser usadas para adicionar telefonia e PSTN às suas aplicações. Esta página resume os principais conceitos e capacidades da telefonia. Consulte a [biblioteca de chamadas](../../quickstarts/voice-video-calling/calling-client-samples.md) para saber mais sobre as línguas e capacidades específicas da biblioteca de clientes.

## <a name="overview-of-telephony"></a>Visão geral da telefonia
Sempre que os seus utilizadores interagem com um número de telefone tradicional, as chamadas são facilitadas pela chamada de voz PSTN (Public Switched Telephone Network). Para escruquisar e receber chamadas PSTN, precisa adicionar capacidades de telefonia ao seu recurso Azure Communication Services. Neste caso, a sinalização e os meios de comunicação utilizam uma combinação de tecnologias baseadas em IP e PSTN para ligar os seus utilizadores. Os Serviços de Comunicação fornecem duas formas discretas de chegar à rede PSTN: Azure Cloud Calling e interface SIP.

### <a name="azure-cloud-calling"></a>Chamada de nuvem de Azure

Uma forma fácil de adicionar conectividade PSTN à sua app ou serviço, neste caso, a Microsoft é o seu fornecedor de telco. Pode comprar números diretamente da Microsoft. A azure Cloud Calling é uma solução de telefonia totalmente na nuvem para serviços de comunicação. Esta é a opção mais simples que liga o ACS à Rede Telefónica Pública Comutada (PSTN) para permitir chamadas para telefones fixos e telemóveis em todo o mundo. Com esta opção, a Microsoft atua como a sua transportadora PSTN, como mostra o seguinte diagrama:

![Diagrama de chamada de nuvem de Azure.](../media/telephony-concept/azure-calling-diagram.png)

Se responder 'sim' ao seguinte, então Azure Cloud Calling é a solução certa para si:
- A chamada de nuvem Azure está disponível na sua região.
- Não precisa de reter o seu atual transportador PSTN.
- Pretende utilizar o acesso gerido pela Microsoft à PSTN.

Com esta opção:
- Obtém-se números diretamente da Microsoft e pode ligar para telefones em todo o mundo.
- Não necessita de implantação ou manutenção de uma implantação no local , porque a chamada da Azure Cloud opera a partir dos Serviços de Comunicação Azure.
- Nota: Se necessário, pode optar por ligar um controlador de fronteira de sessão suportado (SBC) através da Interface SIP para interoperabilidade com PBXs de terceiros, dispositivos analógicos e outros equipamentos de telefonia de terceiros suportados pelo SBC.

Esta opção requer uma ligação ininterrupta aos Serviços de Comunicação Azure.

### <a name="sip-interface"></a>SIP Interface

Com esta opção, pode ligar a telefonia legacy no local e a sua transportadora de eleição aos serviços de Comunicação Azure. Fornece capacidades de chamada PSTN para as suas aplicações ACS, mesmo que o Azure Cloud Calling não esteja disponível no seu país/região. 

![Diagrama da Interface SIP.](../media/telephony-concept/sip-interface-diagram.png)

Se responder 'sim' a qualquer uma das seguintes perguntas, então a SIP Interface é a solução certa para si:

- Pretende utilizar o ACS com capacidades de chamada PSTN.
- Tens de reter o teu atual transportador PSTN.
- Você quer misturar o encaminhamento, com algumas chamadas passando por Azure Cloud Calling, algumas através do seu porta-aviões.
- É necessário interoperar com PBXs de terceiros e/ou equipamentos como pagers, dispositivos analógicos, e assim por diante.

Com esta opção:

- Liga o seu próprio SBC suportado aos Serviços de Comunicação Azure sem a necessidade de software adicional no local.
- Pode utilizar literalmente qualquer transportador de telefonia com ACS.
- Pode optar por configurar e gerir esta opção, ou pode ser configurada e gerida pela sua transportadora ou parceiro (pergunte se a sua transportadora ou parceiro fornece esta opção).
- Pode configurar a interoperabilidade entre o seu equipamento de telefonia , como um PBX de terceiros e dispositivos analógicos - e ACS.

Esta opção requer o seguinte:

- Ligação ininterrupta a Azure.
- Implantação e manutenção de um SBC apoiado.
- Um contrato com uma transportadora de terceiros. (A menos que seja implementada como opção para fornecer uma ligação a PBX de terceiros, dispositivos analógicos ou outros equipamentos de telefonia para utilizadores que estejam nos Serviços de Comunicação.)

## <a name="next-steps"></a>Passos seguintes

### <a name="conceptual-documentation"></a>Documentação conceptual

- [Tipos de números de telefone nos Serviços de Comunicação Azure](./plan-solution.md)
- [Plano para Interface SIP](./sip-interface-infrastructure.md)
- [Preços](../pricing.md)

### <a name="quickstarts"></a>Guias de Início Rápido

- [Obter um número de telefone](../../quickstarts/telephony-sms/get-phone-number.md)
- [Chamada para telefone](../../quickstarts/voice-video-calling/pstn-call.md)
