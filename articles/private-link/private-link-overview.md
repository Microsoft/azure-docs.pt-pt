---
title: O que é a Ligação Privada do Azure?
description: Saiba como utilizar o Azure Private Link para aceder aos Serviços Azure PaaS (por exemplo, Armazenamento Azure e Base de Dados SQL) e o Azure acolheu serviços de clientes/parceiros sobre um Ponto Final Privado na sua rede virtual.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/09/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 88e3077fb64446f5fa70c1785ad3df1d3746de33
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209329"
---
# <a name="what-is-azure-private-link"></a>O que é a Ligação Privada do Azure? 
O Azure Private Link permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Armazenamento Azure, Azure Cosmos DB e SQL Database) e o Azure acolheu serviços de clientes/parceiros sobre um [Ponto Final Privado](private-endpoint-overview.md) na sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Também pode criar o seu próprio [Serviço de Link Privado](private-link-service-overview.md) na sua rede virtual (VNet) e entregá-lo em privado aos seus clientes. A experiência de configuração e consumo utilizando o Azure Private Link é consistente em todos os serviços de parceiros azure PaaS, propriedade do cliente e parceiros partilhados.

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações. Para limitações conhecidas, consulte [Private Endpoint](private-endpoint-overview.md#limitations) e [Private Link Service](private-link-service-overview.md#limitations).


![Visão geral do ponto final privado](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Principais vantagens
A Azure Private Link oferece os seguintes benefícios:  
- **Serviços**de acesso privado na plataforma Azure : Ligue a sua rede virtual a serviços que funcionaem em Azure em privado sem precisar de um endereço IP público na fonte ou destino. Os prestadores de serviços podem prestar os seus serviços em privado na sua própria rede virtual e os consumidores podem aceder a esses serviços privadamente na sua rede virtual local. A plataforma Private Link tratará da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. 
 
- **No local e nas redes peered**: Serviços de acesso em Azure a partir de instalações através de túneis privados de peering/VPN da ExpressRoute (a partir de instalações) e redes virtuais com pontos finais privados. Não há necessidade de criar público sinuoso ou atravessar a internet para chegar ao serviço. Esta capacidade fornece uma forma segura de migrar cargas de trabalho para Azure.
 
- **Proteção contra exfiltração**de dados : Com o Azure Private Link, o ponto final privado no VNet é mapeado para uma instância específica do recurso PaaS do cliente em oposição a todo o serviço. A utilização do ponto final privado os consumidores só podem ligar-se ao recurso específico e não a qualquer outro recurso do serviço. Este mecanismo incorporado proporciona proteção contra riscos de exfiltração de dados. 
 
- **Alcance global**: Ligue-se privadamente a serviços em funcionamento noutras regiões. Isto significa que a rede virtual do consumidor pode estar na região A e pode ligar-se aos serviços por detrás da Private Link na região B.  
 
- **Estenda-se aos seus próprios serviços**: Aproveite a mesma experiência e funcionalidade para prestar o seu próprio serviço em privado aos seus consumidores em Azure. Ao colocar o seu serviço atrás de um Balancer de Carga Padrão, pode ativar para Private Link. O consumidor pode então ligar-se diretamente ao seu serviço utilizando um Ponto Final Privado no seu próprio VNet. Você pode gerir estes pedidos de ligação usando um simples fluxo de chamada de aprovação. A Azure Private Link trabalha para consumidores e serviços pertencentes a diferentes inquilinos do Ative Directory. 

## <a name="availability"></a>Disponibilidade 
 A tabela seguinte lista os serviços de Private Link e as regiões onde estão disponíveis. 

|Cenário  |Serviços suportados  |Regiões disponíveis | Estado  |
|:---------|:-------------------|:-----------------|:--------|
|Link privado para serviços de propriedade do cliente|Serviços de Link Privado por trás do Standard Load Balancer | Todas as regiões públicas  | Pré-visualização  |
|Link privado para serviços Azure PaaS   | Storage do Azure        |  Todas as regiões públicas      | Pré-visualização <br/> [Saiba mais](/azure/storage/common/storage-private-endpoints).  |
|  | Armazenamento do Azure Data Lake Ger2        |  Todas as regiões públicas      | Pré-visualização <br/> [Saiba mais](/azure/storage/common/storage-private-endpoints).  |
|  |  Base de Dados SQL do Azure         | Todas as regiões públicas      |   Pré-visualização      |
||Azure SQL Data Warehouse| Todas as regiões públicas |Pré-visualização|
||Azure Cosmos DB| Centro-Oeste dos EUA, WestUS, Centro Norte dos EUA |Pré-visualização|
|  |  Base de Dados Azure para PostgreSQL - Servidor único         | Todas as regiões públicas      |   Pré-visualização      |
|  |  Base de Dados do Azure para MySQL         | Todas as regiões públicas      |   Pré-visualização      |
|  |  Azure Database for MariaDB         | Todas as regiões públicas      |   Pré-visualização      |
|  |  Azure Key Vault         | Todas as regiões públicas      |   Pré-visualização      |

Para obter as notificações mais atualizadas, consulte a página de [atualizações da Rede Virtual Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

O Azure Private Link está integrado com o Azure Monitor, que permite arquivar registos numa conta de armazenamento, transmitir eventos para o seu Hub de Eventos ou enviá-los para registos do Monitor Azure. Pode aceder às seguintes informações sobre o Monitor Azure: 
- **Ponto final privado**: Dados tratados pelo Ponto Final Privado (IN/OUT)
 
- **Serviço de Ligação Privada:**
    - Dados tratados pelo serviço de Ligação Privada (IN/OUT)
    - Disponibilidade portuária NAT  
 
## <a name="pricing"></a>Preços   
Para obter detalhes sobre preços, consulte o preço do [Link Privado Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Perguntas mais frequentes  
Para perguntas faqs, consulte [As FAQs de Ligação Privada Azure](private-link-faq.md).
 
## <a name="limits"></a>Limites  
Para limites, consulte os limites de [Ligação Privada Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="next-steps"></a>Passos seguintes
- [Criar um ponto final privado para o Servidor de Base de Dados SQL utilizando o Portal](create-private-endpoint-portal.md)
- [Criar um ponto final privado para o Servidor de Base de Dados SQL usando powerShell](create-private-endpoint-powershell.md)
- [Criar um ponto final privado para o Servidor de Base de Dados SQL utilizando o CLI](create-private-endpoint-cli.md)
- [Criar um ponto final privado para a conta de armazenamento usando o Portal](create-private-endpoint-storage-portal.md)
- [Criar um endpoint privado para a conta Azure Cosmos usando o Portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Crie o seu próprio serviço private link usando o Azure PowerShell](create-private-link-service-powershell.md)


 
