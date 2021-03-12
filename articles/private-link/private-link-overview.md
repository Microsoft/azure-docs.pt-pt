---
title: O que é a Ligação Privada do Azure?
description: Visão geral das características do Azure Private Link, arquitetura e implementação. Saiba como funciona o serviço Azure Private Endpoints e Azure Private Link e como utilizá-los.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/28/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: ee9b38343176eec82d8e227e86faa97814f5be13
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102616541"
---
# <a name="what-is-azure-private-link"></a>O que é a Ligação Privada do Azure? 
O Azure Private Link permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Azure Storage e SQL Database) e à Azure aloja serviços de propriedade do cliente/parceiro sobre um [ponto final privado](private-endpoint-overview.md) na sua rede virtual.

O tráfego entre a sua rede virtual e o serviço percorre a rede de espinha dorsal da Microsoft. Expor o seu serviço à internet pública já não é necessário. Pode criar o seu próprio [serviço de ligação privada](private-link-service-overview.md) na sua rede virtual e entregá-lo aos seus clientes. A configuração e consumo usando o Azure Private Link é consistente em todos os serviços Azure PaaS, propriedade do cliente e parceiros partilhados.

> [!IMPORTANT]
> O Azure Private Link está disponível em geral. Tanto o serviço Private Endpoint como o serviço Private Link (serviço por trás do balanceador de carga padrão) estão geralmente disponíveis. O Azure PaaS diferente embarcará no Azure Private Link em diferentes horários. Consulte a secção [de disponibilidade](#availability) neste artigo para obter o estado exato do Azure PaaS no Link Privado. Para limitações conhecidas, consulte [Private Endpoint](private-endpoint-overview.md#limitations) e [Private Link Service](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Centro de Ligação Privada Azure no portal Azure" border="false":::

## <a name="key-benefits"></a>Principais vantagens
A Azure Private Link proporciona os seguintes benefícios:  
- **Serviços de acesso privados na plataforma Azure**: Ligue a sua rede virtual a serviços em Azure sem endereço IP público na fonte ou destino. Os prestadores de serviços podem prestar os seus serviços na sua própria rede virtual e os consumidores podem aceder a esses serviços na sua rede virtual local. A plataforma Private Link tratará da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. 
 
- **No local e redes vigiadas**: Serviços de acesso em funcionamento em Azure a partir de instalações sobre o espreocupamento privado ExpressRoute, túneis VPN e redes virtuais espreitadas utilizando pontos finais privados. Não há necessidade de configurar o ExpressRoute Microsoft a espreitar ou a atravessar a internet para chegar ao serviço. O Private Link fornece uma forma segura de migrar cargas de trabalho para Azure.
 
- **Proteção contra fugas de dados**: Um ponto final privado é mapeado para uma instância de um recurso PaaS em vez de todo o serviço. Os consumidores só podem ligar-se ao recurso específico. O acesso a qualquer outro recurso no serviço está bloqueado. Este mecanismo fornece proteção contra riscos de fuga de dados. 
 
- **Alcance global**: Conecte-se privadamente aos serviços que correm noutras regiões. A rede virtual do consumidor pode estar na região A e pode ligar-se a serviços por trás do Private Link na região B.  
 
- **Estender aos seus próprios serviços**: Ative a mesma experiência e funcionalidade para prestar o seu serviço de forma privada aos consumidores em Azure. Ao colocar o seu serviço atrás de um Balanceador de Carga Azure padrão, pode ative-lo para Private Link. O consumidor pode então ligar-se diretamente ao seu serviço utilizando um ponto final privado na sua própria rede virtual. Pode gerir os pedidos de ligação utilizando um fluxo de chamada de aprovação. A Azure Private Link trabalha para consumidores e serviços pertencentes a diferentes inquilinos do Azure Ative Directory. 

## <a name="availability"></a>Disponibilidade 
 A tabela que se segue lista os serviços de Private Link e as regiões onde estão disponíveis. 

|Serviços suportados  |Regiões disponíveis | Considerações adicionais | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
|Serviços de ligação privada por trás do balanceador de carga padrão Azure | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China  | Suportado no Balanceador de Carga Padrão | GA <br/> [Saiba como criar um serviço de ligação privada.](create-private-link-service-portal.md) |
| Armazenamento Azure Blob (incluindo data lake storage gen2)       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Suportado na Conta Tipo Finalidade Geral V2 | GA <br/> [Aprenda a criar um ponto final privado para armazenamento de bolhas.](tutorial-private-endpoint-storage-portal.md)  |
| Ficheiros do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo      | |   GA <br/> [Saiba como criar pontos finais de rede Azure Files.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Todas as regiões públicas      | |   GA <br/> [Saiba como criar pontos finais de rede Azure Files.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Armazenamento de Filas do Azure       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Suportado na Conta Tipo Finalidade Geral V2 | GA <br/> [Saiba como criar um ponto final privado para armazenamento de filas.](tutorial-private-endpoint-storage-portal.md) |
| Armazenamento de Tabelas do Azure       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Suportado na Conta Tipo Finalidade Geral V2 | GA <br/> [Saiba como criar um ponto final privado para armazenamento de mesas.](tutorial-private-endpoint-storage-portal.md)  |
|  Base de Dados SQL do Azure         | Todas as regiões públicas <br/> Todas as regiões do Governo<br/>Todas as regiões da China      |  Suportado para a política de [ligação](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy | GA <br/> [Saiba como criar um ponto final privado para o Azure SQL](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics| Todas as regiões públicas <br/> Todas as regiões do Governo |  Suportado para a política de [ligação](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy |GA <br/> [Saiba como criar um ponto final privado para a Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Cosmos DB|  Todas as regiões públicas<br/> Todas as regiões do Governo</br> Todas as regiões da China | |GA <br/> [Saiba como criar um ponto final privado para a Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Base de Dados Azure para PostgreSQL - Servidor único         | Todas as regiões públicas <br/> Todas as regiões do Governo<br/>Todas as regiões da China     | Suportado para fins gerais e níveis de preços otimizados de memória | GA <br/> [Saiba como criar um ponto final privado para a Base de Dados Azure para PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Base de Dados do Azure para MySQL         | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China      |  | GA <br/> [Saiba como criar um ponto final privado para a Base de Dados Azure para o MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China     |  | GA <br/> [Saiba como criar um ponto final privado para a Base de Dados Azure para a MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |
|  Azure Digital Twins         | Todas as regiões públicas apoiadas por Azure Digital Twins     |  | Pré-visualizar <br/> [Saiba como criar um ponto final privado para a Azure Digital Twins.](../digital-twins/how-to-enable-private-link-portal.md)      |
|  Azure Key Vault         | Todas as regiões públicas<br/> Todas as regiões do Governo      |  | GA   <br/> [Aprenda a criar um ponto final privado para o Azure Key Vault.](../key-vault/general/private-link-service.md)   |
|Serviço Azure Kubernetes - Kubernetes API | Todas as regiões públicas      |  | GA   <br/> [Saiba como criar um ponto final privado para o Serviço Azure Kubernetes.](../aks/private-clusters.md)   |
|Azure Search | Todas as regiões públicas <br/> Todas as regiões do Governo | Suportado com serviço em Modo Privado | GA   <br/> [Saiba como criar um ponto final privado para a Azure Search.](../search/service-create-private-endpoint.md)    |
|Registo de Contentores do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo    | Suportado com nível premium de registo de contentores. [Selecione para níveis](../container-registry/container-registry-skus.md)| GA   <br/> [Saiba como criar um ponto final privado para o Registo do Contentor Azure.](../container-registry/container-registry-private-link.md)   |
|Configuração da Aplicação Azure | Todas as regiões públicas      |  | Pré-visualizar  </br> [Saiba como criar um ponto final privado para a Configuração da App Azure](../azure-app-configuration/concept-private-endpoint.md) |
|Azure Backup | Todas as regiões públicas<br/> Todas as regiões do Governo   |  | GA   <br/> [Saiba como criar um ponto final privado para o Azure Backup.](../backup/private-endpoints.md)   |
|Hub de Eventos do Azure | Todas as regiões públicas<br/>Todas as regiões do Governo      |   | GA   <br/> [Saiba como criar um ponto final privado para o Azure Event Hub.](../event-hubs/private-link-service.md)  |
|Service Bus do Azure | Todas as regiões públicas<br/>Todas as regiões do Governo  | Suportado com nível premium da Azure Service Bus. [Selecione para níveis](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Saiba como criar um ponto final privado para a Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |
|Reencaminhamento do Azure | Todas as regiões públicas      |  | Pré-visualizar <br/> [Saiba como criar um ponto final privado para a Azure Relay.](../azure-relay/private-link-service.md)  |
|Azure Event Grid| Todas as regiões públicas<br/> Todas as regiões do Governo       |  | GA   <br/> [Saiba como criar um ponto final privado para a Azure Event Grid.](../event-grid/network-security.md) |
|Aplicações Web do Azure | Todas as regiões públicas<br/> China Norte 2 & Leste 2    | Suportado com plano PremiumV2, PremiumV3 ou Function Premium  | GA   <br/> [Saiba como criar um ponto final privado para a Azure Web Apps.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Machine Learning | Todas as regiões públicas    |  | GA   <br/> [Aprenda a criar um ponto final privado para a Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |
| Automatização do Azure  | Todas as regiões públicas<br/> Todas as regiões do Governo |  | Pré-visualizar </br> [Saiba como criar um ponto final privado para a Azure Automation.](../automation/how-to/private-link-security.md)| |
| Azure IoT Hub | Todas as regiões públicas    |  | GA   <br/> [Aprenda a criar um ponto final privado para o Azure IoT Hub.](../iot-hub/virtual-network-support.md) |
| Azure SignalR | LESTE DE NÓS, CENTRO-SUL DE NÓS,<br/>OESTE EUA 2, Todas as regiões da China      |  | Pré-visualizar   <br/> [Saiba como criar um ponto final privado para o Azure SignalR.](../azure-signalr/howto-private-endpoints.md)   |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Todas as regiões públicas      |  | GA   <br/> [Saiba como criar um ponto final privado para o Azure Monitor.](../azure-monitor/logs/private-link-security.md)   | 
| Azure Batch | Todas as regiões públicas, exceto: Alemanha CENTRAL, Alemanha NORDESTE <br/> Todas as regiões do Governo  | | GA <br/> [Saiba como criar um ponto final privado para o Azure Batch.](../batch/private-connectivity.md) |
|Azure Data Factory | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China    | As credenciais precisam de ser armazenadas num cofre de chaves Azure.| GA   <br/> [Saiba como criar um ponto final privado para a Azure Data Factory.](../data-factory/data-factory-private-link.md)   |
|Managed Disks do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China    | [Clique aqui para obter limitações conhecidas](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Saiba como criar um ponto final privado para discos geridos Azure.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |



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
