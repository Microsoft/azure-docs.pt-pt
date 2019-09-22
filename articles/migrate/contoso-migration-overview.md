---
title: Série de migração da contoso | Microsoft Docs
description: Fornece uma visão geral da estratégia de migração e dos cenários usados pela contoso para migrar seu datacenter local para o Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: raynew
ms.openlocfilehash: d20c0be47f44cdce8ea895007494565d37f5923f
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179160"
---
# <a name="contoso-migration-series"></a>Série de migração do Contoso


Temos uma série de artigos que demonstram como a organização fictícia Contoso migra a infraestrutura local para a nuvem [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) . 

A série inclui informações e cenários que ilustram como configurar uma migração de infraestrutura e executar diferentes tipos de migrações. Os cenários crescem em complexidade à medida que eles avançam. Os artigos mostram como a empresa contoso conclui sua missão de migração, mas os ponteiros para leitura geral e instruções específicas são fornecidos em todo o.

## <a name="migration-articles"></a>Artigos de migração

Os artigos da série são resumidos na tabela a seguir.  

- Cada cenário de migração é definido por objetivos comerciais ligeiramente diferentes que determinam a estratégia de migração.
- Para cada cenário de implementação, fornecemos informação sobre os objetivos e os fatores de negócio, uma proposta de arquitetura, indicamos os passos da execução da migração, uma recomendação para limpeza e os passos seguintes após concluída a migração.

**Artigo** | **Detalhes** 
--- | --- 
[Artigo 1: Sobre](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Visão geral da série de artigos, da estratégia de migração da Contoso e dos aplicativos de exemplo que são usados na série. 
[Artigo 2: Implantar a infraestrutura do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | A contoso prepara sua infraestrutura local e sua infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos de migração na série. 
[Artigo 3: Avaliar recursos locais para migração para o Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | A contoso executa uma avaliação de seu aplicativo SmartHotel360 local em execução no VMware. A contoso avalia as VMs de aplicativo usando o serviço de migrações para Azure e o aplicativo SQL Server banco de dados usando Assistente de Migração de Dados.
[Artigo 4: Rehospedar um aplicativo em uma VM do Azure e Instância Gerenciada do Banco de Dados SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | A contoso executa uma migração de comparação de precisão e deslocamento para o Azure para seu aplicativo SmartHotel360 local. A contoso migra a VM de front-end do aplicativo usando [Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). A contoso migra o banco de dados de aplicativo para um Instância Gerenciada do Banco de Dados SQL do Azure usando o [serviço de migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview).
[Artigo 5: Rehospedar um aplicativo em VMs do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | A contoso migra suas VMs de aplicativo SmartHotel360 para VMs do Azure usando o serviço Site Recovery. 
[Artigo 6: Rehospedar um aplicativo em VMs do Azure e em um SQL Server grupo de disponibilidade AlwaysOn](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |A contoso migra o aplicativo SmartHotel360. A contoso usa Site Recovery para migrar as VMs de aplicativo. Ele usa o serviço de migração de banco de dados para migrar o banco de dados de aplicativo para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. 
[Artigo 7: Rehospedar um aplicativo do Linux em VMs do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | A contoso conclui uma migração de comparação entre o seu aplicativo Linux osTicket para VMs do Azure, usando o serviço Site Recovery.
[Artigo 8: Rehospedar um aplicativo do Linux em VMs do Azure e banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | A contoso migra seu aplicativo osTicket do Linux para VMs do Azure usando Site Recovery. Permite migrar a base de dados da aplicação para a Base de Dados do Azure para MySQL com o MySQL Workbench. 
[Artigo 9: Refatorar um aplicativo em um aplicativo Web do Azure e no banco de dados SQL do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | A contoso migra seu aplicativo SmartHotel360 para um aplicativo Web do Azure e migra o banco de dados de aplicativo para uma instância de SQL Server do Azure com o banco de dados Assistente de Migração.     
[Artigo 10: Refatorar um aplicativo do Linux em um aplicativo Web do Azure e no banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | A contoso migra seu aplicativo osTicket do Linux para um aplicativo Web do Azure em várias regiões do Azure usando o Gerenciador de tráfego do Azure, integrado com o GitHub para entrega contínua. A contoso migra o banco de dados de aplicativo para uma instância do banco de dados do Azure para MySQL. 
[Artigo 11: Team Foundation Server de refatoração em Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | A contoso migra sua implantação de Team Foundation Server local para Azure DevOps Services no Azure.
[Artigo 12: Rearquitetar um aplicativo nos contêineres do Azure e no banco de dados SQL do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | A contoso migra seu aplicativo SmartHotel para o Azure. Em seguida, ele rearquiteta a camada da Web do aplicativo como um contêiner do Windows em execução no Azure Service Fabric e o banco de dados com o banco de dados SQL do Azure.
[Artigo 13: Recriar um aplicativo no Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | A contoso recompila seu aplicativo SmartHotel usando uma variedade de recursos e serviços do Azure, incluindo serviço de Azure App, AKS (serviço kubernetes do Azure), Azure Functions, serviços cognitivas do Azure e Azure Cosmos DB.
[Artigo 14: Dimensionar uma migração para o Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Depois de experimentar as combinações de migração, a contoso se prepara para dimensionar uma migração completa para o Azure.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) a migração na nuvem.
- Saiba mais sobre as estratégias de migrações para outros cenários (pares de origem/destino) no [Guia de migração de banco de dados](https://datamigration.microsoft.com/).
