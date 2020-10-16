---
title: O que é a Ligação Privada do Azure?
description: Visão geral das características do Azure Private Link, arquitetura e implementação. Saiba como funciona o serviço Azure Private Endpoints e Azure Private Link e como utilizá-los.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 09/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 1ce0368bb86134bd52473b80eabbfaf87e36148d
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939652"
---
# <a name="what-is-azure-private-link"></a>O que é a Ligação Privada do Azure? 
O Azure Private Link permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Azure Storage e SQL Database) e à Azure aloja serviços de propriedade do cliente/parceiro sobre um [ponto final privado](private-endpoint-overview.md) na sua rede virtual.

O tráfego entre a sua rede virtual e o serviço percorre a rede de espinha dorsal da Microsoft. Expor o seu serviço à internet pública já não é necessário. Pode criar o seu próprio [serviço de ligação privada](private-link-service-overview.md) na sua rede virtual e entregá-lo aos seus clientes. A configuração e consumo usando o Azure Private Link é consistente em todos os serviços Azure PaaS, propriedade do cliente e parceiros partilhados.

> [!IMPORTANT]
> O Azure Private Link está disponível em geral. Tanto o serviço Private Endpoint como o serviço Private Link (serviço por trás do balanceador de carga padrão) estão geralmente disponíveis. O Azure PaaS diferente embarcará no Azure Private Link em diferentes horários. Consulte a secção [de disponibilidade](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) neste artigo para obter o estado exato do Azure PaaS no Link Privado. Para limitações conhecidas, consulte [Private Endpoint](private-endpoint-overview.md#limitations) e [Private Link Service](private-link-service-overview.md#limitations). 

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
| Azure File Sync | Todas as regiões públicas      | |   GA <br/> [Saiba como criar pontos finais de rede Azure Files.](/azure/storage/files/storage-sync-files-networking-endpoints)   |
| Armazenamento de Filas do Azure       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Suportado na Conta Tipo Finalidade Geral V2 | GA <br/> [Saiba como criar um ponto final privado para armazenamento de filas.](tutorial-private-endpoint-storage-portal.md) |
| Armazenamento de Tabelas do Azure       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Suportado na Conta Tipo Finalidade Geral V2 | GA <br/> [Saiba como criar um ponto final privado para armazenamento de mesas.](tutorial-private-endpoint-storage-portal.md)  |
|  Base de Dados SQL do Azure         | Todas as regiões públicas <br/> Todas as regiões do Governo<br/>Todas as regiões da China      |  Suportado para a política de [ligação](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) proxy | GA <br/> [Saiba como criar um ponto final privado para o Azure SQL](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics (anteriormente SQL Data Warehouse)| Todas as regiões públicas <br/> Todas as regiões do Governo |  Suportado para a política de [ligação](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) proxy |GA <br/> [Saiba como criar um ponto final privado para a Azure Synapse Analytics.](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|Azure Cosmos DB|  Todas as regiões públicas<br/> Todas as regiões do Governo</br> Todas as regiões da China | |GA <br/> [Saiba como criar um ponto final privado para a Cosmos DB.](create-private-endpoint-cosmosdb-portal.md)|
|  Base de Dados Azure para PostgreSQL - Servidor único         | Todas as regiões públicas <br/> Todas as regiões do Governo<br/>Todas as regiões da China     | Suportado para fins gerais e níveis de preços otimizados de memória | GA <br/> [Saiba como criar um ponto final privado para a Base de Dados Azure para PostgreSQL.](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  Base de Dados do Azure para MySQL         | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China      |  | GA <br/> [Saiba como criar um ponto final privado para a Base de Dados Azure para o MySQL.](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  Azure Database for MariaDB         | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China     |  | GA <br/> [Saiba como criar um ponto final privado para a Base de Dados Azure para a MariaDB.](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  Azure Key Vault         | Todas as regiões públicas<br/> Todas as regiões do Governo      |  | GA   <br/> [Aprenda a criar um ponto final privado para o Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|Serviço Azure Kubernetes - Kubernetes API | Todas as regiões públicas      |  | GA   <br/> [Saiba como criar um ponto final privado para o Serviço Azure Kubernetes.](https://docs.microsoft.com/azure/aks/private-clusters)   |
|Azure Search | Todas as regiões públicas <br/> Todas as regiões do Governo | Suportado com serviço em Modo Privado | GA   <br/> [Saiba como criar um ponto final privado para a Azure Search.](https://docs.microsoft.com/azure/search/service-create-private-endpoint)    |
|Registo de Contentores do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo    | Suportado com nível premium de registo de contentores. [Selecione para níveis](https://docs.microsoft.com/azure/container-registry/container-registry-skus)| GA   <br/> [Saiba como criar um ponto final privado para o Registo do Contentor Azure.](https://docs.microsoft.com/azure/container-registry/container-registry-private-link)   |
|Configuração da Aplicação Azure | Todas as regiões públicas      |  | Pré-visualizar  </br> [Saiba como criar um ponto final privado para a Configuração da App Azure](https://docs.microsoft.com/azure/azure-app-configuration/concept-private-endpoint) |
|Azure Backup | Todas as regiões públicas<br/> Todas as regiões do Governo   |  | GA   <br/> [Saiba como criar um ponto final privado para o Azure Backup.](https://docs.microsoft.com/azure/backup/private-endpoints)   |
|Hub de Eventos do Azure | Todas as regiões públicas<br/>Todas as regiões do Governo      |   | GA   <br/> [Saiba como criar um ponto final privado para o Azure Event Hub.](https://docs.microsoft.com/azure/event-hubs/private-link-service)  |
|Service Bus do Azure | Todas as regiões públicas<br/>Todas as regiões do Governo  | Suportado com nível premium da Azure Service Bus. [Selecione para níveis](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-premium-messaging) | GA   <br/> [Saiba como criar um ponto final privado para a Azure Service Bus.](https://docs.microsoft.com/azure/service-bus-messaging/private-link-service)    |
|Reencaminhamento do Azure | Todas as regiões públicas      |  | Pré-visualizar <br/> [Saiba como criar um ponto final privado para a Azure Relay.](https://docs.microsoft.com/azure/azure-relay/private-link-service)  |
|Azure Event Grid| Todas as regiões públicas<br/> Todas as regiões do Governo       |  | GA   <br/> [Saiba como criar um ponto final privado para a Azure Event Grid.](https://docs.microsoft.com/azure/event-grid/network-security) |
|Aplicações Web do Azure | Todas as regiões públicas      | Suportado com plano PremiumV2, PremiumV3 ou Function Premium  | GA   <br/> [Saiba como criar um ponto final privado para a Azure Web Apps.](https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal)   |
|Azure Machine Learning | Todas as regiões públicas    |  | GA   <br/> [Aprenda a criar um ponto final privado para a Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |
| Automatização do Azure  | Todas as regiões públicas |  | Pré-visualizar </br> [Saiba como criar um ponto final privado para a Azure Automation.](https://docs.microsoft.com/azure/automation/how-to/private-link-security)| |
| Hub IoT do Azure | Todas as regiões públicas    |  | GA   <br/> [Aprenda a criar um ponto final privado para o Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub/virtual-network-support ) |
| Azure SignalR | LESTE DE NÓS, CENTRO-SUL DE NÓS,<br/>OESTE EUA 2, Todas as regiões da China      |  | Pré-visualizar   <br/> [Saiba como criar um ponto final privado para o Azure SignalR.](https://docs.microsoft.com/azure/azure-signalr/howto-private-endpoints)   |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Todas as regiões públicas      |  | GA   <br/> [Saiba como criar um ponto final privado para o Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security)   | 
| Azure Batch | Todas as regiões públicas, exceto: Alemanha CENTRAL, Alemanha NORDESTE <br/> Todas as regiões do Governo  | | GA <br/> [Saiba como criar um ponto final privado para o Azure Batch.](https://docs.microsoft.com/azure/batch/private-connectivity) |
|Azure Data Factory | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China    | As credenciais precisam de ser armazenadas num cofre de chaves Azure.| GA   <br/> [Saiba como criar um ponto final privado para a Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/data-factory-private-link)   |



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


 
