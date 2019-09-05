---
title: Serviços do Azure em escopo de auditoria FedRAMP e DoD SRG
description: Este artigo contém tabelas para o Azure público e o Azure governamental que ilustram o que FedRAMP (moderado versus Alto) e DoD SRG (nível de impacto 2, 4 ou 5) o escopo de auditoria um determinado serviço foi atingido.
author: davib
ms.author: davib
ms.date: 5/17/2019
ms.topic: article
ms.service: security
ms.reviewer: rochiou
ms.openlocfilehash: bdf63601e8456a2b99cee655872138ad97afe47f
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375324"
---
# <a name="azure-services-by-fedramp-and-dod-cc-srg-audit-scope"></a>Serviços do Azure por FedRAMP e o escopo de auditoria SRG do DoD CC

Os serviços de nuvem do governo da Microsoft atendem aos requisitos exigentes do FedRAMP (programa de gerenciamento de autorização) de & risco Federal dos EUA e do departamento de defesa dos EUA, desde os níveis de impacto de informações de 2 a 5. Ao implantar serviços protegidos, incluindo o Azure governamental, o Office 365 nos EUA O governo e as agências governamentais, federais e de defesa do Dynamics 365 podem aproveitar uma ampla gama de serviços em conformidade.

Este artigo fornece uma lista detalhada dos serviços de nuvem dentro do escopo do Azure e do Azure governamental para ofertas de conformidade do FedRAMP e do DoD CC SRG.

#### <a name="terminologysymbols-used"></a>Terminologia/símbolos usados

* DoD CC SRG = departamento de defesa guia de requisitos de segurança de computação em nuvem
* IL = nível de impacto
* FedRAMP = Federal Risk and Authorization Management Program  
* : heavy_check_mark: = indica que o serviço atingiu esse escopo de auditoria.
* Planejado 2019 = indica que a revisão do serviço está em andamento e irá obter o certificado em 2019.

## <a name="azure-public-services-by-audit-scope"></a>Serviços públicos do Azure por escopo de auditoria
| _Última atualização: Agosto de 2019_ |

| Serviço do Azure| IL de SRG CC de DoD 2 | FedRAMP Moderado | FedRAMP Elevado | 2019 planejado |
| ------------ |:---------------:|:----------------:|:------------:|:------------:|
| [Gestão de API](https://azure.microsoft.com/services/api-management/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Gateway de Aplicação](https://azure.microsoft.com/services/application-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Automatização](https://azure.microsoft.com/services/automation/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory (gratuito e básico)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory (Premium P1 + P2)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) |  |  |  | :heavy_check_mark: |
| [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) | |  |  | :heavy_check_mark: |
| [Proteção avançada contra ameaças do Azure](https://azure.microsoft.com/features/azure-advanced-threat-protection/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Assistente do Azure](https://azure.microsoft.com/services/advisor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [API do Azure para FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) |  |  |  | :heavy_check_mark: |
| [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Armazenamento de Arquivos do Azure](https://azure.microsoft.com/services/storage/archive/) |  |  |  | :heavy_check_mark: |
| [Serviço de contêiner do Azure](https://docs.microsoft.com/azure/container-service/) |  |  |  | :heavy_check_mark: |
| [BD do Cosmos para o Azure](https://azure.microsoft.com/services/cosmos-db/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Gestão de Custos do Azure](https://azure.microsoft.com/services/cost-management/) | |  |  | :heavy_check_mark: |
| [Azure Data Box](https://azure.microsoft.com/services/databox/) | |  |  | :heavy_check_mark: |
| [Data Explorer do Azure](https://azure.microsoft.com/services/data-explorer/) | |  |  | :heavy_check_mark: |
| [Armazenamento do Azure Data Lake Ger1](https://azure.microsoft.com/services/storage/data-lake-storage/) |  |  |  | :heavy_check_mark: |
| [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Base de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Database for MariaDB](https://azure.microsoft.com/services/mariadb/) |  |  |  | :heavy_check_mark: |
| [Serviço de migração de base de dados do Azure](https://azure.microsoft.com/services/database-migration/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) |  |  |  |  |
| [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) |  |  |  | :heavy_check_mark: |
| [Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [HSM dedicado do Azure](https://azure.microsoft.com/services/azure-dedicated-hsm/) |  |  |  | :heavy_check_mark: |
| [Azure DevOps (anteriormente VSTS)](https://azure.microsoft.com/services/devops/) | |  |  | |
| [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) |  |  |  | :heavy_check_mark: |
| [DNS do Azure](https://azure.microsoft.com/services/dns/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure File Sync](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) |  |  |  | :heavy_check_mark: |
| [Porta frontal do Azure](https://azure.microsoft.com/services/frontdoor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Intune](https://docs.microsoft.com/intune/what-is-intune) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviço Kubernetes do Azure (AKS)](https://azure.microsoft.com/services/kubernetes-service/) | |  |  | :heavy_check_mark: |
| [Aplicativos gerenciados do Azure](https://azure.microsoft.com/services/managed-applications/) | |  |  | :heavy_check_mark: |
| [Azure Maps](https://azure.microsoft.com/services/azure-maps/) |  |  |  | :heavy_check_mark: |
| [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Monitor](https://azure.microsoft.com/services/monitor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Policy](https://azure.microsoft.com/services/azure-policy/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Search](https://azure.microsoft.com/services/search/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Service Manager do Azure (RDFE)](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Sentinela do Azure](https://azure.microsoft.com/services/azure-sentinel/) |  |  |  | :heavy_check_mark: |
| [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cópia de segurança](https://azure.microsoft.com/services/backup/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Batch](https://azure.microsoft.com/services/batch/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços Cloud](https://azure.microsoft.com/services/cloud-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivas: Imagem Digitalizada](https://azure.microsoft.com/services/cognitive-services/computer-vision/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivas: Content Moderator](https://azure.microsoft.com/services/cognitive-services/content-moderator/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivas: Visão Personalizada](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivas: Face](https://azure.microsoft.com/services/cognitive-services/face/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivas: Reconhecimento vocal](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivas: QnA Maker](https://azure.microsoft.com/services/cognitive-services/qna-maker/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivas: Serviços de fala](https://azure.microsoft.com/services/cognitive-services/directory/speech/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivas: Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivas: Texto do Tradutor](https://azure.microsoft.com/services/cognitive-services/speech-translation/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivas: Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Container Instances](https://azure.microsoft.com/services/container-instances/) |  |  |  | :heavy_check_mark: |
| [Container Registry](https://azure.microsoft.com/services/container-registry/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Rede de distribuição de conteúdo](https://azure.microsoft.com/services/cdn/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Catálogo de Dados](https://azure.microsoft.com/services/data-catalog/) |  |  |  | :heavy_check_mark: |
| [Data Factory](https://azure.microsoft.com/services/data-factory/) |  |  |  | :heavy_check_mark: |
| [Event Grid](https://azure.microsoft.com/services/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Proteção avançada contra ameaças do defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Fluxo](https://docs.microsoft.com/flow/getting-started) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Funções](https://azure.microsoft.com/services/functions/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Importação/exportação](https://azure.microsoft.com/services/storage/import-export/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Centro de IoT](https://azure.microsoft.com/services/iot-central/) |  |  |  | :heavy_check_mark: |
| [Hub IoT](https://azure.microsoft.com/services/iot-hub/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cofre de Chaves](https://azure.microsoft.com/services/key-vault/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Balanceador de Carga](https://azure.microsoft.com/services/load-balancer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Aplicações Lógicas](https://azure.microsoft.com/services/logic-apps/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços de Machine Learning](https://azure.microsoft.com/services/machine-learning-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/) |  |  |  | :heavy_check_mark: |
| [Serviços de Multimédia](https://azure.microsoft.com/services/media-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [portal do Microsoft Azure](https://azure.microsoft.com/features/azure-portal/)| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Genomics](https://azure.microsoft.com/services/genomics/) |  |  |  | :heavy_check_mark: |
| [Microsoft PowerApps](https://docs.microsoft.com/powerapps/powerapps-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Stream](https://docs.microsoft.com/stream/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Observador de rede](https://azure.microsoft.com/services/network-watcher/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Análise de Tráfego do observador de rede](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hubs de Notificação](https://azure.microsoft.com/services/notification-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cache de Redis](https://azure.microsoft.com/services/cache/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Scheduler](https://azure.microsoft.com/services/scheduler/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Centro de Segurança](https://azure.microsoft.com/services/security-center/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Service Bus](https://azure.microsoft.com/services/service-bus/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Service Fabric](https://azure.microsoft.com/services/service-fabric/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Base de Dados SQL](https://azure.microsoft.com/services/sql-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [SQL Server Stretch Database](https://azure.microsoft.com/services/sql-server-stretch-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Repositório BLOBs](https://azure.microsoft.com/services/storage/blobs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Repositório Discos (incluindo Managed Disks)](https://azure.microsoft.com/services/storage/disks/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Repositório Arquivos](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Repositório Filas](https://azure.microsoft.com/services/storage/queues/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Repositório Tabelas](https://azure.microsoft.com/services/storage/tables/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [StorSimple](https://azure.microsoft.com/services/storsimple/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Gestor de Tráfego](https://azure.microsoft.com/services/traffic-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Conjuntos de Dimensionamento de Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Máquinas virtuais (incluindo instâncias reservadas)](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Rede Virtual](https://azure.microsoft.com/services/virtual-network/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Gateway de VPN](https://azure.microsoft.com/services/vpn-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Aplicativos Web (serviço de aplicativo)](https://azure.microsoft.com/services/app-service/web/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="azure-government-services-by-audit-scope"></a>Serviços do Azure governamental por escopo de auditoria
| _Última atualização: Agosto de 2019_ |

| Serviço do Azure | IL de SRG CC de DoD 2 | SRG de IL do DoD CC 4 | DoD CC SRG IL 5 | FedRAMP Elevado | 2019 planejado
| ------------- |:---------------:|:---------------:|:---------------:|:------------:|:------------:
| [Gestão de API](https://azure.microsoft.com/services/api-management/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de Aplicação](https://azure.microsoft.com/services/application-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) |  |  |  |  | :heavy_check_mark:
| [Automatização](https://azure.microsoft.com/services/automation/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: |
| [Azure Active Directory (gratuito e básico)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Active Directory (Premium P1 + P2)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Proteção avançada contra ameaças do Azure](https://azure.microsoft.com/features/azure-advanced-threat-protection/)  |  |  |  |  | :heavy_check_mark:
| [Assistente do Azure](https://azure.microsoft.com/services/advisor/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Blueprints](https://azure.microsoft.com/services/blueprints/)  |  |  |  |  | :heavy_check_mark:
| [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/) |  |  |  |  | :heavy_check_mark:
| [Armazenamento de Arquivos do Azure](https://azure.microsoft.com/services/storage/archive/) |  |  |  |  | :heavy_check_mark:
| [Container Registry](https://azure.microsoft.com/services/container-registry/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [BD do Cosmos para o Azure](https://azure.microsoft.com/services/cosmos-db/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gestão de Custos do Azure](https://azure.microsoft.com/services/cost-management/)  |  |  |  |  | :heavy_check_mark:
| [Azure Data Box Edge](https://azure.microsoft.com/services/databox/edge/) |  |  |  |  | :heavy_check_mark:
| [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)  | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [BD do Azure para MySQL](https://azure.microsoft.com/services/mysql/)| :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [BD do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [BD do Azure para MariaDB](https://azure.microsoft.com/services/mariadb/)  | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/) |  |  |  |  | :heavy_check_mark:
| [HSM dedicado do Azure](https://azure.microsoft.com/services/azure-dedicated-hsm/) |  |  |  |  | :heavy_check_mark:
| [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/)  | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [DNS do Azure](https://azure.microsoft.com/services/dns/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)  | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure File Sync](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) |  |  |  |  | :heavy_check_mark:
| [Porta frontal do Azure](https://azure.microsoft.com/services/frontdoor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Intune](https://docs.microsoft.com/intune/what-is-intune) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Serviço Kubernetes do Azure (AKS)](https://azure.microsoft.com/services/kubernetes-service/) |  |  |  |  | :heavy_check_mark:
| [Azure Lab Services](https://azure.microsoft.com/services/lab-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Aplicativos gerenciados do Azure](https://azure.microsoft.com/services/managed-applications/) |  |  |  |  | :heavy_check_mark:
| [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Monitor](https://azure.microsoft.com/services/monitor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Policy](https://azure.microsoft.com/services/azure-policy/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: **&ast;** | :heavy_check_mark: |
| [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Service Manager do Azure (RDFE)](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Cópia de segurança](https://azure.microsoft.com/services/backup/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Batch](https://azure.microsoft.com/services/batch/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Serviços Cloud](https://azure.microsoft.com/services/cloud-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Serviços cognitivas: Imagem Digitalizada](https://azure.microsoft.com/services/cognitive-services/computer-vision/)  | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Serviços cognitivas: Content Moderator](https://azure.microsoft.com/services/cognitive-services/content-moderator/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Serviços cognitivas: Visão Personalizada](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) |  |  |  |  | :heavy_check_mark:
| [Serviços cognitivas: Face](https://azure.microsoft.com/services/cognitive-services/face/)  | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Serviços cognitivas: Reconhecimento vocal](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Serviços cognitivas: Serviços de fala](https://azure.microsoft.com/services/cognitive-services/directory/speech/) |  |  |  |  | :heavy_check_mark:
| [Serviços cognitivas: Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Serviços cognitivas: Texto do Tradutor](https://azure.microsoft.com/services/cognitive-services/speech-translation/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Fluxo](https://docs.microsoft.com/flow/getting-started) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Funções](https://azure.microsoft.com/services/functions/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Importação/exportação](https://azure.microsoft.com/services/storage/import-export/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Hub IoT](https://azure.microsoft.com/services/iot-hub/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Cofre de Chaves](https://azure.microsoft.com/services/key-vault/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](https://azure.microsoft.com/services/load-balancer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Aplicações Lógicas](https://azure.microsoft.com/services/logic-apps/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Serviços de Multimédia](https://azure.microsoft.com/services/media-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [portal do Microsoft Azure](https://azure.microsoft.com/features/azure-portal/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: **&ast;** | :heavy_check_mark: |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) |  |  |  |  | :heavy_check_mark:
| [Proteção avançada contra ameaças do Microsoft defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Microsoft Graph](https://docs.microsoft.com/graph/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Microsoft PowerApps](https://docs.microsoft.com/powerapps/powerapps-overview) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Microsoft Stream](https://docs.microsoft.com/stream/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Observador de rede](https://azure.microsoft.com/services/network-watcher/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Hubs de Notificação](https://azure.microsoft.com/services/notification-hubs/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Cache de Redis](https://azure.microsoft.com/services/cache/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Scheduler](https://azure.microsoft.com/services/scheduler/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](https://azure.microsoft.com/services/service-bus/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://azure.microsoft.com/services/service-fabric/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Base de Dados SQL](https://azure.microsoft.com/services/sql-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [SQL Server Stretch Database](https://azure.microsoft.com/services/sql-server-stretch-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Repositório BLOBs](https://azure.microsoft.com/services/storage/blobs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Repositório Discos (incluindo Managed Disks)](https://azure.microsoft.com/services/storage/disks/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Repositório Arquivos](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Repositório Filas](https://azure.microsoft.com/services/storage/queues/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Repositório Tabelas](https://azure.microsoft.com/services/storage/tables/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [StorSimple](https://azure.microsoft.com/services/storsimple/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) |  |  |  |  | :heavy_check_mark:
| [Gestor de Tráfego](https://azure.microsoft.com/services/traffic-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de Dimensionamento de Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](https://azure.microsoft.com/services/virtual-network/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](https://azure.microsoft.com/services/vpn-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Aplicativos Web (serviço de aplicativo)](https://azure.microsoft.com/services/app-service/web/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

**&ast;** Alguns serviços como o portal do Azure e Azure Resource Manager não armazenam ou processam conteúdo DoD CC SRG IIL 5, mas ainda herdam o conjunto de controle IIL5.

