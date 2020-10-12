---
title: Série de migração contoso Microsoft Docs
description: Ligações a Contoso exemplo cenários de migração, para migração para Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: f9f7b3b64cf91019ed4e40d5d52b859419b74836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86107636"
---
# <a name="contoso-migration-series"></a>Série de migração do Contoso


Temos uma série de artigos que demonstram como a organização fictícia Contoso migra a sua infraestrutura no local para a nuvem [microsoft Azure.](https://azure.microsoft.com/overview/what-is-azure/) 

A série inclui cenários que ilustram como configurar uma migração de infraestruturas, e como executar diferentes tipos de migrações. Os cenários crescem em complexidade à medida que progridem. Os artigos mostram como a empresa Contoso lida com a migração, mas instruções gerais e ponteiros são fornecidos em todo o lado.

## <a name="migration-articles"></a>Artigos de migração

Os artigos da série são resumidos na tabela abaixo.  

- Cada cenário de migração é impulsionado por requisitos de negócio ligeiramente diferentes, que determinam a estratégia de migração.
- Para cada cenário de implantação, fornecemos informações sobre motoristas de negócios e objetivos, uma arquitetura proposta, passos para realizar a migração, e recomendações para limpeza e próximos passos após a migração está completa.


**Artigo** | **Detalhes** 
--- | --- 
[Artigo 1º: Visão geral](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Visão geral da série de artigos, estratégia de migração de Contoso e as aplicações de amostra que são usadas na série. 
[Artigo 2.º: Implantar infraestruturas Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | A Contoso prepara as suas infraestruturas no local e a infraestrutura Azure para a migração. A mesma infraestrutura é utilizada para todos os artigos da série. 
[Artigo 3º: Avaliar os recursos para a migração para Azure](/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | A Contoso faz uma avaliação da sua aplicação smartHotel360 no local, em execução na VMware. Contoso avalia os VMs da aplicação utilizando o serviço Azure Migrate e a base de dados do Servidor SQL da aplicação usando o Data Migration Assistant.
[Artigo 4.º: Reencauma aplicação sobre um Azure VM e SQL Managed Instance](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | A Contoso gere uma migração de elevador e mudança para a Azure para a sua aplicação SmartHotel360 no local. Contoso migra a aplicação front-end VM usando [Azure Migrate](./migrate-services-overview.md). A Contoso migra a base de dados de aplicações para uma SQL Managed Instance, utilizando o [Serviço de Migração da Base de Dados Azure.](../dms/dms-overview.md)
[Artigo 5.º: Reencaúndio de uma aplicação sobre VMs Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | A Contoso migra os seus VMs de aplicação SmartHotel360 para Azure VMs utilizando o serviço Azure Migrate. 
[Artigo 6.º: Reencauma uma aplicação sobre VMs Azure e num grupo de disponibilidade sql Server AlwaysOn](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso migra a aplicação SmartHotel360. Contoso usa Azure Migrate para migrar os VMs da aplicação. Utiliza o Serviço de Migração da Base de Dados para migrar a base de dados de aplicações para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. 
[Artigo 7.º: Rehost a Linux app em Azure VMs](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | A Contoso completa uma migração de elevador e mudança da sua app Linux osTicket para Azure VMs, utilizando a Azure Migrate.
[Artigo 8.º: Rehost a App Linux em VMs Azure e Azure Database for MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | A Contoso migra a sua app Linux osTicket para Azure VMs usando Azure Migrate. Migra a base de dados de aplicações para Azure Database para o MySQ, utilizando o Azure Database Migration Service (inclui uma opção alternativa utilizando a bancada mySQL Workbench).
[Artigo 9.º: Refactor de uma aplicação numa aplicação web Azure e na Base de Dados Azure SQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | A Contoso migra a sua aplicação SmartHotel360 para uma aplicação web Azure e migra a base de dados de aplicações para a Base de Dados Azure SQL, utilizando o Serviço de Migração da Base de Dados de Bases de Dados Azure.
[Artigo 10.º: Refactor a app Windows utilizando os Serviços de Aplicações Azure e a SQL Managed Instance](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | A Contoso migra uma aplicação baseada no Windows para uma aplicação web Azure e migra a base de dados de aplicações para uma Instância Gerida Azure SQL, utilizando o Serviço de Migração da Base de Dados Azure.
[Artigo 11.º: Refactor a app Linux numa aplicação web Azure e base de dados Azure para o MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | A Contoso migra a sua app Linux osTicket para uma aplicação web Azure em várias regiões do Azure, utilizando o Azure Traffic Manager, integrado com o GitHub para entrega contínua. Contoso migra a base de dados de aplicações para uma Base de Dados Azure para o caso MySQL. 
[Artigo 12.º: Servidor da Fundação Refactor Team em Serviços Azure DevOps](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | A Contoso migra a sua implementação no local do Team Foundation Server para os Serviços Azure DevOps em Azure.
[Artigo 13.º: Reconstruir uma aplicação em Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | A Contoso reconstrói a sua aplicação SmartHotel utilizando uma gama de capacidades e serviços Azure, incluindo o Azure App Service, o Azure Kubernetes Service (AKS), o Azure Functions, o Azure Cognitive Services e o Azure Cosmos DB.
[Artigo 14.º: Escalar uma migração para Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Depois de experimentar combinações de migração, Contoso prepara-se para escalar para uma migração completa para Azure.



## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre](/azure/architecture/cloud-adoption/migrate/) migração em nuvem.
- Conheça as estratégias de migração para outros cenários (pares de origem/alvo) no [Guia de Migração da Base de Dados.](https://datamigration.microsoft.com/)
