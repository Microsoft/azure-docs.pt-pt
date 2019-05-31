---
title: Série de migração da Contoso | Documentos da Microsoft
description: Fornece uma descrição geral da estratégia de migração e cenários usados pela Contoso para migrar o seu datacenter no local para o Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: raynew
ms.openlocfilehash: bf2a8dec919980db616cd927639cdd2f695720c5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238015"
---
# <a name="contoso-migration-series"></a>Série de migração do Contoso


Temos uma série de artigos demonstra como a organização fictícia, Contoso migra infraestrutura no local para o [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) na cloud. 

A série contém informações e cenários que ilustram como configurar uma migração da infraestrutura e executar diferentes tipos de migrações. Cenários de crescer em complexidade à medida que progride. Os artigos mostram como a empresa Contoso conclui sua missão de migração, mas ponteiros para leitura geral e instruções específicas são fornecidos ao longo.

## <a name="migration-articles"></a>Artigos de migração

Os artigos da série estão resumidos na tabela abaixo.  

- Cada cenário de migração é orientado por objetivos de negócio ligeiramente diferente que determinam a estratégia de migração.
- Para cada cenário de implantação, fornecemos informações sobre controladores de negócios e objetivos, uma arquitetura proposta, passos para efetuar a migração e as recomendações para a limpeza e os passos seguintes após a conclusão da migração.

**Artigo** | **Detalhes** 
--- | --- 
[Artigo 1: Descrição geral](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Descrição geral da série de artigos, estratégia de migração da Contoso e as aplicações de exemplo que são utilizadas da série. 
[Artigo 2: Implementar a infraestrutura do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso prepara a sua infraestrutura no local e a respetiva infraestrutura do Azure para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração da série. 
[Artigo 3: Avalie os recursos no local para migração para o Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso é executada uma avaliação da sua aplicação da SmartHotel360 no local em execução no VMware. Contoso avalia as VMs de aplicação com o serviço Azure Migrate e a base de dados de SQL Server do aplicativo com o Assistente de migração de dados.
[Artigo 4: Realojar a uma aplicação numa VM do Azure e a instância gerida da base de dados do SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso é executada uma migração lift-and-shift para o Azure para a sua aplicação da SmartHotel360 no local. Contoso migra a aplicação front-end VM com [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migra a base de dados de aplicação para uma instância gerida da base de dados SQL do Azure utilizando o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[5 do artigo: Realojar a uma aplicação em VMs do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migra seu aplicativo da SmartHotel360 VMs para as VMs do Azure com o serviço de recuperação de sites. 
[Artigo 6: Realojar a uma aplicação em VMs do Azure e, num grupo de Disponibilidade AlwaysOn do SQL Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migra a aplicação da SmartHotel360. A Contoso utiliza o Site Recovery para migrar as VMs da aplicação. Ele usa o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server que está protegido por um grupo de Disponibilidade AlwaysOn. 
[Artigo 7: Realojar uma aplicação do Linux em VMs do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso é concluída uma migração lift-and-shift de seu aplicativo de osTicket do Linux para VMs do Azure, com o serviço Site Recovery.
[Artigo 8: Realojar uma aplicação do Linux em VMs do Azure e base de dados do Azure para MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migra sua osTicket de aplicação do Linux para VMs do Azure com o Site Recovery. Ele migra a base de dados de aplicação à base de dados do Azure para MySQL com o MySQL Workbench. 
[Artigo 9: Refatorizar uma aplicação numa aplicação web do Azure e base de dados do Azure SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migra a sua aplicação da SmartHotel360 para uma aplicação web do Azure e migra a base de dados de aplicação para uma instância de servidor SQL do Azure com o Assistente de migração de base de dados.     
[Artigo 10: Refatorizar uma aplicação do Linux numa aplicação web do Azure e base de dados do Azure para MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migra sua osTicket de aplicação do Linux para uma aplicação web do Azure em várias regiões do Azure utilizando o Gestor de tráfego do Azure, integrado com o GitHub para a entrega contínua. Contoso migra a base de dados de aplicação para uma base de dados do Azure para a instância do MySQL. 
[Artigo 11: Refatorar o Team Foundation Server nos serviços de DevOps do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migra sua implantação do Team Foundation Server no local para serviços do Azure DevOps no Azure.
[Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados do Azure SQL](c https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migra sua SmartHotel de aplicação para o Azure. Em seguida, rearchitects a camada de web de aplicação como um contentor do Windows em execução no Azure Service Fabric e base de dados com a base de dados do Azure SQL. 
[Artigo 13: Recriar uma aplicação no Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso recria o seu aplicativo de SmartHotel com uma variedade de capacidades do Azure e serviços, incluindo o serviço de aplicações do Azure, Azure Kubernetes Service (AKS), as funções do Azure, serviços cognitivos do Azure e Azure Cosmos DB.  
[Artigo 14: Dimensionar uma migração para o Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. 


    

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migração para a cloud. 

