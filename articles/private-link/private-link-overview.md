---
title: O que é a Ligação Privada do Azure?
description: Visão geral das características do Azure Private Link, arquitetura e implementação. Saiba como funciona o serviço Azure Private Endpoints e Azure Private Link e como utilizá-los.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 03/15/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 6a85bfe7b3390b32fc220000b0c710b5a4e35067
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496495"
---
# <a name="what-is-azure-private-link"></a>O que é a Ligação Privada do Azure? 
O Azure Private Link permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Azure Storage e SQL Database) e à Azure aloja serviços de propriedade do cliente/parceiro sobre um [ponto final privado](private-endpoint-overview.md) na sua rede virtual.

O tráfego entre a sua rede virtual e o serviço percorre a rede de espinha dorsal da Microsoft. Expor o seu serviço à internet pública já não é necessário. Pode criar o seu próprio [serviço de ligação privada](private-link-service-overview.md) na sua rede virtual e entregá-lo aos seus clientes. A configuração e consumo usando o Azure Private Link é consistente em todos os serviços Azure PaaS, propriedade do cliente e parceiros partilhados.

> [!IMPORTANT]
> O Azure Private Link está disponível em geral. Tanto o serviço Private Endpoint como o serviço Private Link (serviço por trás do balanceador de carga padrão) estão geralmente disponíveis. O Azure PaaS diferente embarcará no Azure Private Link em diferentes horários. Consulte [a disponibilidade do Link Privado](availability.md) para obter um estado exato do Azure PaaS no Link Privado. Para limitações conhecidas, consulte [Private Endpoint](private-endpoint-overview.md#limitations) e [Private Link Service](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Centro de Ligação Privada Azure no portal Azure" border="false":::

## <a name="key-benefits"></a>Principais vantagens
A Azure Private Link proporciona os seguintes benefícios:  
- **Serviços de acesso privados na plataforma Azure**: Ligue a sua rede virtual a serviços em Azure sem endereço IP público na fonte ou destino. Os prestadores de serviços podem prestar os seus serviços na sua própria rede virtual e os consumidores podem aceder a esses serviços na sua rede virtual local. A plataforma Private Link tratará da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. 
 
- **No local e redes vigiadas**: Serviços de acesso em funcionamento em Azure a partir de instalações sobre o espreocupamento privado ExpressRoute, túneis VPN e redes virtuais espreitadas utilizando pontos finais privados. Não há necessidade de configurar o ExpressRoute Microsoft a espreitar ou a atravessar a internet para chegar ao serviço. O Private Link fornece uma forma segura de migrar cargas de trabalho para Azure.
 
- **Proteção contra fugas de dados**: Um ponto final privado é mapeado para uma instância de um recurso PaaS em vez de todo o serviço. Os consumidores só podem ligar-se ao recurso específico. O acesso a qualquer outro recurso no serviço está bloqueado. Este mecanismo fornece proteção contra riscos de fuga de dados. 
 
- **Alcance global**: Conecte-se privadamente aos serviços que correm noutras regiões. A rede virtual do consumidor pode estar na região A e pode ligar-se a serviços por trás do Private Link na região B.  
 
- **Estender aos seus próprios serviços**: Ative a mesma experiência e funcionalidade para prestar o seu serviço de forma privada aos consumidores em Azure. Ao colocar o seu serviço atrás de um Balanceador de Carga Azure padrão, pode ative-lo para Private Link. O consumidor pode então ligar-se diretamente ao seu serviço utilizando um ponto final privado na sua própria rede virtual. Pode gerir os pedidos de ligação utilizando um fluxo de chamada de aprovação. A Azure Private Link trabalha para consumidores e serviços pertencentes a diferentes inquilinos do Azure Ative Directory. 

## <a name="availability"></a>Disponibilidade 

Para obter informações sobre os serviços Azure que suportam o Private Link, consulte [a disponibilidade do Link Privado Azure](availability.md).

Para obter as notificações mais atualizadas, consulte a página de atualizações do [Link Privado Azure](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

A Azure Private Link tem integração com o Azure Monitor. Esta combinação permite:

 - Arquivo de registos para uma conta de armazenamento.
 - Streaming de eventos para o seu Centro de Eventos.
 - Registos do Azure Monitor.

Pode aceder às seguintes informações no Azure Monitor: 
- **Ponto final privado:** 
    - Dados tratados pelo Private Endpoint (IN/OUT)
 
- **Serviço de Ligação Privada:**
    - Dados tratados pelo serviço Private Link (IN/OUT)
    - Disponibilidade da porta NAT  
 
## <a name="pricing"></a>Preços   
Para obter detalhes sobre os preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>FAQs  
Para perguntas frequentes, consulte [as FAQs de ligação privada Azure.](private-link-faq.md)
 
## <a name="limits"></a>Limites  
Para limites, consulte [os limites da Ligação Privada Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Acordo de Nível de Serviço
Para sLA, consulte [sLA para Azure Private Link](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Criar um ponto final privado utilizando o portal Azure](create-private-endpoint-portal.md)
- [Quickstart: Criar um serviço de ligação privada utilizando o portal Azure](create-private-link-service-portal.md)
