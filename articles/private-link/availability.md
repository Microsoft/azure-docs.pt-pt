---
title: Disponibilidade de Link Privado Azure
description: Neste artigo, saiba quais os serviços Azure que suportam o Private Link.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 26485c84749b7d4c91159476b3f683c2b0f3831b
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555681"
---
# <a name="azure-private-link-availability"></a>Disponibilidade de Link Privado Azure

O Azure Private Link permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Azure Storage e SQL Database) e à Azure aloja serviços de propriedade do cliente/parceiro sobre um [ponto final privado](private-endpoint-overview.md) na sua rede virtual. 

> [!IMPORTANT]
> O Azure Private Link está disponível em geral. Tanto o serviço Private Endpoint como o serviço Private Link (serviço por trás do balanceador de carga padrão) estão geralmente disponíveis. O Azure PaaS diferente embarcará no Azure Private Link em diferentes horários. Para limitações conhecidas, consulte [Private Endpoint](private-endpoint-overview.md#limitations) e [Private Link Service](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Disponibilidade de serviço

As tabelas a seguir listam os serviços de Private Link e as regiões onde estão disponíveis. 

### <a name="ai--machine-learning"></a>IA + Machine Learning

|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Todas as regiões públicas    |  | GA   <br/> [Aprenda a criar um ponto final privado para a Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Análise

|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Todas as regiões públicas <br/> Todas as regiões do Governo |  Suportado para a política de [ligação](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy |GA <br/> [Saiba como criar um ponto final privado para a Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Hub de Eventos do Azure | Todas as regiões públicas<br/>Todas as regiões do Governo      |   | GA   <br/> [Saiba como criar um ponto final privado para o Azure Event Hub.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Todas as regiões públicas      |  | GA   <br/> [Saiba como criar um ponto final privado para o Azure Monitor.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China    | As credenciais precisam de ser armazenadas num cofre de chaves Azure.| GA   <br/> [Saiba como criar um ponto final privado para a Azure Data Factory.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Computação

|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
|Configuração da Aplicação Azure | Todas as regiões públicas      |  | Pré-visualizar  </br> [Saiba como criar um ponto final privado para a Configuração da App Azure](../azure-app-configuration/concept-private-endpoint.md) |
|Discos geridos pelo Azure | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China    | [Selecione para limitações conhecidas](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Saiba como criar um ponto final privado para discos geridos Azure.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Contentores

|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
|Registo de Contentores do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo    | Suportado com nível premium de registo de contentores. [Selecione para níveis](../container-registry/container-registry-skus.md)| GA   <br/> [Saiba como criar um ponto final privado para o Registo do Contentor Azure.](../container-registry/container-registry-private-link.md)   |
|Serviço Azure Kubernetes - Kubernetes API | Todas as regiões públicas      |  | GA   <br/> [Saiba como criar um ponto final privado para o Serviço Azure Kubernetes.](../aks/private-clusters.md)   |

### <a name="databases"></a>Bases de Dados

|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
|  Base de Dados SQL do Azure         | Todas as regiões públicas <br/> Todas as regiões do Governo<br/>Todas as regiões da China      |  Suportado para a política de [ligação](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy | GA <br/> [Saiba como criar um ponto final privado para o Azure SQL](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Todas as regiões públicas<br/> Todas as regiões do Governo</br> Todas as regiões da China | |GA <br/> [Saiba como criar um ponto final privado para a Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Base de Dados Azure para PostgreSQL - Servidor único         | Todas as regiões públicas <br/> Todas as regiões do Governo<br/>Todas as regiões da China     | Suportado para fins gerais e níveis de preços otimizados de memória | GA <br/> [Saiba como criar um ponto final privado para a Base de Dados Azure para PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Base de Dados do Azure para MySQL         | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China      |  | GA <br/> [Saiba como criar um ponto final privado para a Base de Dados Azure para o MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China     |  | GA <br/> [Saiba como criar um ponto final privado para a Base de Dados Azure para a MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integração

|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Todas as regiões públicas<br/> Todas as regiões do Governo       |  | GA   <br/> [Saiba como criar um ponto final privado para a Azure Event Grid.](../event-grid/network-security.md) |
|Service Bus do Azure | Todas as regiões públicas<br/>Todas as regiões do Governo  | Suportado com nível premium da Azure Service Bus. [Selecione para níveis](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Saiba como criar um ponto final privado para a Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Internet das Coisas (IoT)

|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Todas as regiões públicas    |  | GA   <br/> [Aprenda a criar um ponto final privado para o Azure IoT Hub.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Todas as regiões públicas apoiadas por Azure Digital Twins     |  | Pré-visualizar <br/> [Saiba como criar um ponto final privado para a Azure Digital Twins.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Gestão e Governação

| Serviços suportados | Regiões disponíveis | Outras considerações | Estado  |
| ------------ | ----------------| ------------| ----------------|
| Automatização do Azure  | Todas as regiões públicas<br/> Todas as regiões do Governo |  | Pré-visualizar </br> [Saiba como criar um ponto final privado para a Azure Automation.](../automation/how-to/private-link-security.md)|
|Azure Backup | Todas as regiões públicas<br/> Todas as regiões do Governo   |  | GA <br/> [Saiba como criar um ponto final privado para o Azure Backup.](../backup/private-endpoints.md)   |

### <a name="security"></a>Segurança

|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Todas as regiões públicas<br/> Todas as regiões do Governo      |  | GA   <br/> [Aprenda a criar um ponto final privado para o Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Armazenamento
|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
| Armazenamento Azure Blob (incluindo data lake storage gen2)       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Suportado na Conta Tipo Finalidade Geral V2 | GA <br/> [Aprenda a criar um ponto final privado para armazenamento de bolhas.](tutorial-private-endpoint-storage-portal.md)  |
| Ficheiros do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo      | |   GA <br/> [Saiba como criar pontos finais de rede Azure Files.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Todas as regiões públicas      | |   GA <br/> [Saiba como criar pontos finais de rede Azure Files.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Armazenamento de Filas do Azure       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Suportado na Conta Tipo Finalidade Geral V2 | GA <br/> [Saiba como criar um ponto final privado para armazenamento de filas.](tutorial-private-endpoint-storage-portal.md) |
| Armazenamento de Tabelas do Azure       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Suportado na Conta Tipo Finalidade Geral V2 | GA <br/> [Saiba como criar um ponto final privado para armazenamento de mesas.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Todas as regiões públicas, exceto: Alemanha CENTRAL, Alemanha NORDESTE <br/> Todas as regiões do Governo  | | GA <br/> [Saiba como criar um ponto final privado para o Azure Batch.](../batch/private-connectivity.md) |

### <a name="web"></a>Web
|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | LESTE DE NÓS, CENTRO-SUL DE NÓS,<br/>OESTE EUA 2, Todas as regiões da China      |  | Pré-visualizar   <br/> [Saiba como criar um ponto final privado para o Azure SignalR.](../azure-signalr/howto-private-endpoints.md)   |
|Aplicações Web do Azure | Todas as regiões públicas<br/> China Norte 2 & Leste 2    | Suportado com plano PremiumV2, PremiumV3 ou Function Premium  | GA   <br/> [Saiba como criar um ponto final privado para a Azure Web Apps.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Todas as regiões públicas <br/> Todas as regiões do Governo | Suportado com serviço em Modo Privado | GA   <br/> [Saiba como criar um ponto final privado para a Azure Search.](../search/service-create-private-endpoint.md)    |
|Reencaminhamento do Azure | Todas as regiões públicas      |  | Pré-visualizar <br/> [Saiba como criar um ponto final privado para a Azure Relay.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Serviço de Ligação Privada com um balanceador de carga padrão

|Serviços suportados  |Regiões disponíveis | Outras considerações | Estado  |
|:-------------------|:-----------------|:----------------|:--------|
|Serviços de ligação privada por trás do balanceador de carga padrão Azure | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China  | Suportado no Balanceador de Carga Padrão | GA <br/> [Saiba como criar um serviço de ligação privada.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o serviço Azure Private Link:
- [O que é a Ligação Privada do Azure?](private-link-overview.md)
- [Criar um Ponto Final Privado com o portal do Azure](create-private-endpoint-portal.md)
