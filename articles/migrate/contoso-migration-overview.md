---
title: Contoso série de migração [ Contoso] Microsoft Docs
description: Ligações a cenários de migração exemplo Contoso, para migração para Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676316"
---
# <a name="contoso-migration-series"></a>Série de migração do Contoso


Temos uma série de artigos que demonstram como a organização fictícia Contoso migra a sua infraestrutura no local para a nuvem do [Microsoft Azure.](https://azure.microsoft.com/overview/what-is-azure/) 

A série inclui cenários que ilustram como criar uma migração de infraestruturas e como executar diferentes tipos de migrações. Os cenários crescem em complexidade à medida que progridem. Os artigos mostram como a empresa Contoso lida com a migração, mas instruções gerais e ponteiros são fornecidos ao longo de todo.

## <a name="migration-articles"></a>Artigos de migração

Os artigos da série são resumidos na tabela abaixo.  

- Cada cenário de migração é impulsionado por requisitos de negócio ligeiramente diferentes, que determinam a estratégia de migração.
- Para cada cenário de implantação, fornecemos informações sobre motoristas e objetivos empresariais, uma arquitetura proposta, passos para realizar a migração, e recomendações para limpeza e próximos passos após a migração estar concluída.


**Artigo** | **Detalhes** 
--- | --- 
[Artigo 1º: Visão geral](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Visão geral da série de artigos, estratégia de migração de Contoso, e as aplicações de amostra que são usadas na série. 
[Artigo 2º: Implantação da infraestrutura Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso prepara a sua infraestrutura no local e infraestruturas Azure para migração. A mesma infraestrutura é utilizada para todos os artigos da série. 
[Artigo 3.º: Avaliar os recursos no local para a migração para Otáuo](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | A Contoso faz uma avaliação da sua aplicação no local SmartHotel360 em execução em VMware. Contoso avalia os VMs da aplicação utilizando o serviço Azure Migrate e a base de dados da app SQL Server utilizando o Data Migration Assistant.
[Artigo 4.º: Realojar uma aplicação sobre uma instância gerida por bases de dados Azure VM e SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | A Contoso realiza uma migração de elevadores e mudanças para o Azure para a sua aplicação no local SmartHotel360. Contoso migra a aplicação front-end VM usando [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview). Contoso migra a base de dados da aplicação para uma instância gerida pela Base de Dados Azure SQL, utilizando o Serviço de Migração de Bases de [Dados Azure](https://docs.microsoft.com/azure/dms/dms-overview).
[Artigo 5º: Reacolher uma aplicação sobre VMs Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migra a sua aplicação SmartHotel360 VMs para VMs Azure usando o serviço Azure Migrate. 
[Artigo 6.º: Realojar uma aplicação em VMs Azure e num grupo de disponibilidade sQL Server AlwaysOn](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso migra a aplicação SmartHotel360. Contoso usa Azure Migrate para migrar os VMs da aplicação. Utiliza o Serviço de Migração de Bases de Dados para migrar a base de dados da aplicação para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. 
[Artigo 7º: Reacolher uma aplicação Linux sobre VMs Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso completa uma migração de elevador e mudança da sua aplicação Linux osTicket para VMs Azure, usando o Azure Migrate.
[Artigo 8.º: Reacolher uma aplicação Linux sobre VMs Azure e Base de Dados Azure para MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migra a sua aplicação Linux osTicket para Os VMs Azure usando o Azure Migrate. Migra a base de dados de aplicações para a Base de Dados Azure para o MySQ, utilizando o Serviço de Migração de Bases de Dados Azure (inclui uma opção alternativa utilizando a bancada de trabalho MySQL).
[Artigo 9.º: Refactor uma aplicação numa aplicação web do Azure e na Base de Dados Azure SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migra a sua aplicação SmartHotel360 para uma aplicação web Azure, e migra a base de dados de aplicações para uma instância do Servidor Azure SQL, utilizando o Serviço de Migração da Base de Dados Azure.
[Artigo 10.º: Refactor a aplicação Windows utilizando serviços de aplicações do Azure e Instância Gerida SQL](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso migra uma aplicação baseada no Windows no local para uma aplicação web Azure, e migra a base de dados de aplicações para uma Instância Gerida Azure SQL, utilizando o Serviço de Migração de Bases de Dados Azure.
[Artigo 11.º: Refactor a Linux app em uma aplicação web Azure e Base de Dados Azure para MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migra a sua aplicação Linux osTicket para uma aplicação web Azure em várias regiões do Azure, utilizando o Azure Traffic Manager, integrado com o GitHub para entrega contínua. Contoso migra a base de dados de aplicações para uma base de dados Azure para a instância MySQL. 
[Artigo 12.º: Refactor Team Foundation Server sobre serviços Azure DevOps](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migra a sua implantação no local Team Foundation Server para a Azure DevOps Services em Azure.
[Artigo 13.º: Reconstruir uma aplicação em Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | A Contoso reconstrói a sua aplicação SmartHotel utilizando uma gama de capacidades e serviços Azure, incluindo o Azure App Service, o Azure Kubernetes Service (AKS), o Azure Functions, o Azure Cognitive Services e o Azure Cosmos DB.
[Artigo 14º: Escala de uma migração para Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Depois de experimentar combinações de migração, Contoso prepara-se para escalar para uma migração completa para Azure.



## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migração em nuvem.
- Conheça as estratégias de migração para outros cenários (par de origem/alvo) no Guia de [Migração](https://datamigration.microsoft.com/)da Base de Dados .
