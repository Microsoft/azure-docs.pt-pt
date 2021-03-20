---
title: Visão geral do Serviço de Observação Azure
description: Saiba mais sobre a visão geral do Serviço de Peering Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026734"
---
# <a name="azure-peering-service-overview"></a>Visão geral do serviço de peering Azure

O Azure Peering Service é um serviço de networking que melhora a conectividade do cliente com os serviços na nuvem da Microsoft, tais como o Microsoft 365, Dynamics 365, software como serviço (SaaS), Azure ou quaisquer serviços da Microsoft acessíveis através da internet pública. A Microsoft estabeleceu uma parceria com fornecedores de serviços de internet (ISPs), parceiros de intercâmbio de internet (IXPs) e fornecedores de interligação de nuvem definida por software (SDCI) em todo o mundo para fornecer conectividade pública fiável e de alto desempenho com o encaminhamento ideal do cliente para a rede Microsoft.

Com o Serviço de Escondamento, os clientes podem selecionar um prestador de serviços parceiros bem conectado numa determinada região. A conectividade pública é otimizada para alta fiabilidade e latência mínima dos serviços na nuvem até à localização do utilizador final.

![Conectividade distribuída para a nuvem da Microsoft](./media/peering-service-about/peering-service-what.png)

Os clientes também podem optar pela telemetria peering Service, como medidas de latência do utilizador para a rede Microsoft, monitorização de rotas BGP e alertas contra fugas e sequestros, registando a ligação do Serviço de Pares no portal Azure. 

Para utilizar o Serviço de Peering, os clientes não são obrigados a registar-se com a Microsoft. O único requisito é contactar um [parceiro do Serviço de Apoio](location-partners.md) à Espreita para obter o serviço. Para optar pela telemetria do Serviço de Observação, os clientes devem inscrever-se no portal Azure.

Para obter instruções sobre como registar o Serviço de Peritagem, consulte [o Serviço de Permumento registado através do portal Azure](azure-portal.md). 

> [!NOTE]
> Este artigo destina-se a arquitetos de rede encarregados da conectividade empresarial com a nuvem e com a internet.


## <a name="what-is-peering-service"></a>O que é o Peering Service?

O Serviço de Observação é:

- Um serviço IP que utiliza a internet pública. 
- Uma plataforma de colaboração com fornecedores de serviços e um serviço de valor acrescentado que se destina a oferecer um encaminhamento ideal e fiável ao cliente através de parceiros de prestadores de serviços para a nuvem da Microsoft sobre a rede pública.

O Serviço de Observação não é um produto de conectividade privada como o Azure ExpressRoute ou um produto VPN.

> [!NOTE]
> Para mais informações sobre o ExpressRoute, consulte a [documentação ExpressRoute](../expressroute/index.yml).
>

## <a name="background"></a>Fundo

Microsoft 365, Dynamics 365 e quaisquer outros serviços Microsoft SaaS estão hospedados em vários datacenters da Microsoft e podem ser acedidos a partir de qualquer localização geográfica. A rede global da Microsoft tem localizações do Ponto de Presença (PoP) do Microsoft Edge em todo o mundo, onde pode ligar-se a um utilizador final através dos seus fornecedores de serviços. 

A Microsoft e os fornecedores de serviços de parceiros asseguram que o tráfego dos prefixos registados com uma ligação peering Service entra e sai das localizações poP do Microsoft Edge mais próximas na rede global da Microsoft. A Microsoft garante que a saída de tráfego de rede a partir dos prefixos registados nas ligações peering Service leva as localizações poP do Microsoft Edge mais próximas na rede global da Microsoft.

![Rede Microsoft e conectividade pública](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Para obter mais informações sobre a rede global da Microsoft, consulte a [rede global da Microsoft.](../networking/microsoft-global-network.md)
>

## <a name="why-use-peering-service"></a>Porquê usar o Serviço de Observação?

As empresas que procuram acesso à nuvem em primeiro lugar ou que considerem a arquitetura SD-WAN ou com uma elevada utilização dos serviços microsoft SaaS precisam de uma conectividade de internet robusta e de alto desempenho. Os clientes podem fazer essa transição acontecer utilizando o Serviço de Peering. A Microsoft e os fornecedores de serviços estabeleceram uma parceria para fornecer conectividade pública fiável e centrada no desempenho à nuvem da Microsoft. Algumas das principais funcionalidades do cliente estão listadas aqui:

- Melhor encaminhamento público através da internet para o Microsoft Azure Cloud Services para um desempenho e fiabilidade ideais.
- Capacidade de selecionar o prestador de serviços preferido para ligar à nuvem microsoft.
- Informações de tráfego, tais como relatórios de latência e monitorização de prefixos.
- Lúpulo de rede ideal (lúpulo AS) da nuvem microsoft.
- Análise de rotas e estatísticas: Eventos para anomalias[de](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)rota (deteção de fugas ou sequestros) e encaminhamento subóptima.

### <a name="robust-reliable-peering"></a>Um olhar robusto e fiável

O Serviço de Observação utiliza dois tipos de redundância:

- **Redundância local**

   A Microsoft e os fornecedores de serviços interligam-se em várias localizações do Microsoft Edge PoP para fornecer o Serviço de Peering. Em cada local, a interligação deve suportar o failover em dois routers.

   Cada local de observação é a provisionado com ligações de espreitamento redundantes e diversificadas.

- **Geodusiunância**

   A Microsoft interligou-se com fornecedores de serviços em vários locais do metro para que, se um dos nós edge tiver degradado o desempenho, as rotas de tráfego de e para a Microsoft através de sites alternativos. A Microsoft encaminha o tráfego na sua rede global utilizando políticas de encaminhamento baseadas em SDN para um melhor desempenho.

    Este tipo de redundância utiliza o caminho de encaminhamento mais curto, escolhendo sempre o Microsoft Edge PoP mais próximo para o utilizador final e garante que o cliente está a um salto de rede (AS hops) longe da Microsoft.

   ![Georredundância](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>Encaminhamento ideal

É preferível a seguinte técnica de encaminhamento:

-  **Encaminhamento de batatas frias**

   A técnica de encaminhamento de batatas frias definida pelo software oferece controlo sobre o tráfego de rede que se origina da nuvem da Microsoft. Garante que o tráfego permanece na rede global da Microsoft de alta capacidade, baixa latência e altamente confiável até que esteja o mais próximo possível do destino.
   
   O encaminhamento que não utiliza a técnica de batata fria é referido como encaminhamento de batata-quente. Com o encaminhamento de batatas quentes, o tráfego que se origina da nuvem da Microsoft passa depois pela internet.

   ![Encaminhamento de batatas frias](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>Plataforma de monitorização

   A monitorização do serviço é oferecida para analisar o tráfego e encaminhamento do cliente, e fornece as seguintes capacidades: 

-  **Deteção de anomalias na rota BGP da Internet**
          
   Este serviço é utilizado para detetar e alertar para quaisquer eventos de anomalia de rota, como sequestros de rota para os prefixos do cliente.

-  **Latência do cliente**

   Este serviço monitoriza o desempenho do encaminhamento entre a localização do cliente e a Microsoft. 
   
   O desempenho do encaminhamento é medido através da validação do tempo de ida e volta do cliente para chegar ao Microsoft Edge PoP. Os clientes podem ver os relatórios de latência para diferentes localizações geográficas.

   A monitorização captura os eventos em caso de degradação do serviço.

   ![Plataforma de monitorização do Serviço de Peering](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>Proteção contra o tráfego

O encaminhamento só acontece através de um caminho preferido que é definido quando o cliente está registado no Serviço de Observação.

A Microsoft garante a rota do tráfego através de caminhos preferenciais, mesmo que seja detetada atividade maliciosa.

As anomalias da rota BGP são reportadas no portal Azure, se houver.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre as ligações do Serviço de Peering, consulte [as ligações do Serviço de Peering](connection.md).
- Para saber mais sobre a telemetria de conexão peering Service, consulte [a telemetria de conexão peering service](connection-telemetry.md).
- Para encontrar um parceiro de prestador de [serviços, consulte parceiros e locais do Serviço de Apoio à Espreita.](location-partners.md)
- Para embarcar numa ligação de Serviço de Peering, consulte o [modelo de Serviço de Peering onboarding](onboarding-model.md).
- Para registar uma ligação utilizando o portal Azure, consulte [registar uma ligação de Serviço de Pares utilizando o portal Azure](azure-portal.md).
- Para medir a telemetria, consulte [a telemetria de ligação medida](measure-connection-telemetry.md).