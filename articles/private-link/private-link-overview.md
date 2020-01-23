---
title: O que é a Ligação Privada do Azure?
description: Saiba como usar o link privado do Azure para acessar os serviços de PaaS do Azure (por exemplo, armazenamento do Azure e banco de dados SQL) e serviços de cliente/parceiro hospedados no Azure por meio de um ponto de extremidade privado em sua rede virtual.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/09/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 79a8119f1cd43e36ad639cd292afef5c4a4ed740
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513546"
---
# <a name="what-is-azure-private-link-preview"></a>O que é a Ligação Privada do Azure? (Pré-visualização)
O link privado do Azure permite que você acesse os serviços de PaaS do Azure (por exemplo, armazenamento do Azure, Azure Cosmos DB e banco de dados SQL) e os serviços hospedados de cliente/parceiro do Azure por meio de um [ponto de extremidade privado](private-endpoint-overview.md) em sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você também pode criar seu próprio [serviço de vínculo privado](private-link-service-overview.md) em sua rede virtual (VNet) e fornecê-lo de forma privada aos seus clientes. A experiência de configuração e consumo usando o link privado do Azure é consistente entre os serviços de parceiro de PaaS do Azure, de Propriedade do cliente e de parceiros compartilhados.

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações. Para obter limitações conhecidas, consulte [ponto de extremidade privado](private-endpoint-overview.md#limitations) e [serviço de vínculo privado](private-link-service-overview.md#limitations).


![Visão geral do ponto de extremidade privado](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Principais vantagens
O link privado do Azure oferece os seguintes benefícios:  
- **Acesse os serviços de forma privada na plataforma Azure**: Conecte sua rede virtual a serviços em execução no Azure de modo privado sem precisar de um endereço IP público na origem ou no destino. Os provedores de serviços podem renderizar seus serviços de forma privada em sua própria rede virtual e os consumidores podem acessar esses serviços de forma privada em sua rede virtual local. A plataforma de link privado manipulará a conectividade entre o consumidor e os serviços na rede de backbone do Azure. 
 
- **Redes emparelhadas e locais**: serviços de acesso em execução no Azure local por meio de túneis de VPN/emparelhamento privado do ExpressRoute (do local) e redes virtuais emparelhadas usando pontos de extremidade privados. Não é necessário configurar o emparelhamento público ou atravessar a Internet para acessar o serviço. Essa capacidade fornece uma maneira segura de migrar cargas de trabalho para o Azure.
 
- **Proteção contra data vazamento**: com o link privado do Azure, o ponto de extremidade privado na VNet é mapeado para uma instância específica do recurso de PaaS do cliente, em oposição ao serviço inteiro. Usar o ponto de extremidade privado os consumidores só podem se conectar ao recurso específico e não a nenhum outro recurso no serviço. Esse mecanismo interno fornece proteção contra riscos de vazamento de dados. 
 
- **Alcance global**: Conecte-se de forma privada aos serviços em execução em outras regiões. Isso significa que a rede virtual do consumidor pode estar na região A e pode se conectar aos serviços por trás do link privado na região B.  
 
- **Estenda-se para seus próprios serviços**: Aproveite a mesma experiência e a mesma funcionalidade para processar seu próprio serviço de forma privada para seus consumidores no Azure. Colocando seu serviço por trás de um Standard Load Balancer você pode habilitá-lo para o link privado. O consumidor pode, então, se conectar diretamente ao seu serviço usando um ponto de extremidade privado em sua própria VNet. Você pode gerenciar essas solicitações de conexão usando um fluxo de chamada de aprovação simples. O link privado do Azure funciona para consumidores e serviços que pertencem a locatários diferentes do Active Directory também. 

## <a name="availability"></a>Disponibilidade 
 A tabela a seguir lista os serviços de vínculo privado e as regiões em que estão disponíveis. 

|Cenário  |Serviços suportados  |Regiões disponíveis | Estado  |
|:---------|:-------------------|:-----------------|:--------|
|Link privado para serviços de Propriedade do cliente|Serviços de vínculo privado por trás Standard Load Balancer | Todas as regiões públicas  | Pré-visualização  |
|Link privado para os serviços de PaaS do Azure   | Armazenamento do Azure        |  Todas as regiões públicas      | Pré-visualização <br/> [Saiba mais](/azure/storage/common/storage-private-endpoints).  |
|  | Ger2 de Armazenamento do Azure Data Lake        |  Todas as regiões públicas      | Pré-visualização <br/> [Saiba mais](/azure/storage/common/storage-private-endpoints).  |
|  |  Base de Dados SQL do Azure         | Todas as regiões públicas      |   Pré-visualização      |
||Armazém de Dados SQL do Azure| Todas as regiões públicas |Pré-visualização|
||Azure Cosmos DB| Oeste EUA Central, Westus, norte EUA Central |Pré-visualização|
|  |  Banco de dados do Azure para PostgreSQL-servidor único         | Todas as regiões públicas      |   Pré-visualização      |
|  |  Base de Dados do Azure para MySQL         | Todas as regiões públicas      |   Pré-visualização      |
|  |  Azure Database for MariaDB         | Todas as regiões públicas      |   Pré-visualização      |
Para obter as notificações mais atualizadas, verifique a [página atualizações de rede virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

O link privado do Azure é integrado com Azure Monitor que permite que você arquive logs em uma conta de armazenamento, transmita eventos para o Hub de eventos ou envie-os para Azure Monitor logs. Você pode acessar as seguintes informações em Azure Monitor: 
- **Ponto de extremidade privado**: dados processados pelo ponto de extremidade privado (in/out)
 
- **Serviço de link privado**:
    - Dados processados pelo serviço de vínculo privado (entrada/saída)
    - Disponibilidade da porta NAT  
 
## <a name="pricing"></a>Preços   
Para obter detalhes de preços, consulte [preço do link privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>FAQ  
Para perguntas frequentes, consulte [perguntas frequentes sobre link privado do Azure](private-link-faq.md).
 
## <a name="limits"></a>Limites  
Para limites, consulte [limites de link privado do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="next-steps"></a>Passos seguintes
- [Criar um ponto de extremidade privado para o servidor de banco de dados SQL usando o portal](create-private-endpoint-portal.md)
- [Criar um ponto de extremidade privado para o servidor de banco de dados SQL usando o PowerShell](create-private-endpoint-powershell.md)
- [Criar um ponto de extremidade privado para o servidor de banco de dados SQL usando a CLI](create-private-endpoint-cli.md)
- [Criar um ponto de extremidade privado para a conta de armazenamento usando o portal](create-private-endpoint-storage-portal.md)
- [Criar um ponto de extremidade privado para a conta do Azure Cosmos usando o portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Crie seu próprio serviço de vínculo privado usando Azure PowerShell](create-private-link-service-powershell.md)


 
