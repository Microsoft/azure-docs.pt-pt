---
title: O que é a Ligação Privada do Azure?
description: Visão geral das funcionalidades, arquitetura e implementação do Azure Private Link. Saiba como funciona o serviço Azure Private Endpoints e azure Private Link e como usá-los.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 8d226b67c0b438ac726fc3abf6452db68fb10dce
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79140710"
---
# <a name="what-is-azure-private-link"></a>O que é a Ligação Privada do Azure? 
O Azure Private Link permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Armazenamento Azure e Base de Dados SQL) e o Azure acolheu serviços de clientes/parceiros sobre um [ponto final privado](private-endpoint-overview.md) na sua rede virtual.

O tráfego entre a sua rede virtual e o serviço viaja pela rede de espinha dorsal da Microsoft. Expor o seu serviço à internet pública já não é necessário. Pode criar o seu próprio [serviço de ligação privada](private-link-service-overview.md) na sua rede virtual e entregá-lo aos seus clientes. A configuração e consumo utilizando o Azure Private Link é consistente em todos os serviços de parceiros azure PaaS, propriedade do cliente e parceiros partilhados.

> [!IMPORTANT]
> O Azure Private Link está agora geralmente disponível. Tanto o serviço Private Endpoint como o Private Link (serviço por trás do equilíbrio de carga padrão) estão geralmente disponíveis. O Azure PaaS diferente estará a bordo do Azure Private Link em diferentes horários. Verifique a secção [de disponibilidade](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) abaixo para obter o estado exato do Azure PaaS no Private Link. Para limitações conhecidas, consulte [Private Endpoint](private-endpoint-overview.md#limitations) e [Private Link Service](private-link-service-overview.md#limitations). 

![Visão geral do ponto final privado](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Principais vantagens
A Azure Private Link oferece os seguintes benefícios:  
- **Serviços**de acesso privado na plataforma Azure : Ligue a sua rede virtual a serviços em Azure sem endereço IP público na fonte ou destino. Os prestadores de serviços podem prestar os seus serviços na sua própria rede virtual e os consumidores podem aceder a esses serviços na sua rede virtual local. A plataforma Private Link tratará da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. 
 
- **No local e redes peered**: Serviços de acesso em Azure a partir de instalações através de peering privado ExpressRoute, túneis VPN e redes virtuais espreitadas usando pontos finais privados. Não há necessidade de criar público ou atravessar a internet para chegar ao serviço. A Private Link fornece uma forma segura de migrar cargas de trabalho para O Azure.
 
- **Proteção contra fugas**de dados : Um ponto final privado é mapeado para uma instância de um recurso PaaS em vez de todo o serviço. Os consumidores só podem ligar-se ao recurso específico. O acesso a qualquer outro recurso do serviço está bloqueado. Este mecanismo proporciona proteção contra riscos de fuga de dados. 
 
- **Alcance global**: Ligue-se privadamente a serviços em funcionamento noutras regiões. A rede virtual do consumidor pode estar na região A e pode ligar-se a serviços por detrás da Private Link na região B.  
 
- **Estenda-se aos seus próprios serviços**: Ative a mesma experiência e funcionalidade para prestar o seu serviço em privado aos consumidores do Azure. Ao colocar o seu serviço atrás de um Balancer de Carga Azure padrão, pode ative-o para Private Link. O consumidor pode então ligar-se diretamente ao seu serviço utilizando um ponto final privado na sua própria rede virtual. Pode gerir os pedidos de ligação utilizando um fluxo de chamada de aprovação. A Azure Private Link trabalha para consumidores e serviços pertencentes a diferentes inquilinos do Azure Ative Directory. 

## <a name="availability"></a>Disponibilidade 
 A tabela seguinte lista os serviços de Private Link e as regiões onde estão disponíveis. 

|Cenário  |Serviços suportados  |Regiões disponíveis | Estado  |
|:---------|:-------------------|:-----------------|:--------|
|Link privado para serviços de propriedade do cliente|Serviços de Link Privado por trás do standard Azure Load Balancer | Todas as regiões públicas  | GA <br/> [Saiba mais](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Link privado para serviços Azure PaaS   | Storage do Azure        |  Todas as regiões públicas      | Pré-visualização <br/> [Saiba mais](/azure/storage/common/storage-private-endpoints)  |
|  | Armazenamento do Azure Data Lake Ger2        |  Todas as regiões públicas      | Pré-visualização <br/> [Saiba mais](/azure/storage/common/storage-private-endpoints)  |
|  |  Base de Dados SQL do Azure         | Todas as regiões públicas      |   Pré-visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
||Azure SQL Data Warehouse| Todas as regiões públicas |Pré-visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
||Azure Cosmos DB| Centro-Oeste dos EUA, WestUS, Centro Norte dos EUA |Pré-visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Base de Dados Azure para PostgreSQL - Servidor único         | Todas as regiões públicas      |   Pré-visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Base de Dados do Azure para MySQL         | Todas as regiões públicas      |   Pré-visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Azure Database for MariaDB         | Todas as regiões públicas      |   Pré-visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  Azure Key Vault         | Todas as regiões públicas      |   Pré-visualização   <br/> [Saiba mais](https://docs.microsoft.com/azure/key-vault/private-link-service)   |

Para obter as notificações mais atualizadas, consulte a página de [atualizações da Rede Virtual Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

A Azure Private Link tem integração com o Azure Monitor. Esta combinação permite:

 - Arquivo de registos para uma conta de armazenamento.
 - Streaming de eventos para o seu Hub de Eventos.
 - Exploração madeireira do Monitor Azure.

Pode aceder às seguintes informações sobre o Monitor Azure: 
- **Ponto final privado:** 
    - Dados tratados pelo Ponto Final Privado (IN/OUT)
 
- **Serviço de Ligação Privada:**
    - Dados tratados pelo serviço de Ligação Privada (IN/OUT)
    - Disponibilidade portuária NAT  
 
## <a name="pricing"></a>Preços   
Para obter detalhes sobre preços, consulte o preço do [Link Privado Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Perguntas mais frequentes  
Para perguntas faqs, consulte [As FAQs de Ligação Privada Azure](private-link-faq.md).
 
## <a name="limits"></a>Limites  
Para limites, consulte os limites de [Ligação Privada Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Acordo de Nível de Serviço
Para sLA, consulte [SLA para Azure Private Link](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Criar um endpoint privado usando o portal Azure](create-private-endpoint-portal.md)
- [Quickstart: Criar um serviço de Link Privado utilizando o portal Azure](create-private-link-service-portal.md)


 
