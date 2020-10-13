---
title: Visão geral do SQL Server Sempre Em grupos de disponibilidade
description: Este artigo introduz o SQL Server Always On availability groups em Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 34d76d7c85a478b5e31a692e653752aa1653884c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293667"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Introduzindo o SQL Server Sempre em grupos de disponibilidade em Máquinas Virtuais Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo introduz grupos de disponibilidade de servidor SQL em Máquinas Virtuais Azure. 

Sempre Em grupos de disponibilidade em Azure Virtual Machines são semelhantes a Always On grupos de disponibilidade nas instalações. Para obter mais informações, consulte [Sempre Em Grupos de Disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

O diagrama seguinte ilustra as partes de um grupo completo de disponibilidade de servidor SQL em Máquinas Virtuais Azure.

![Grupo de Disponibilidade](./media/availability-group-overview/00-EndstateSampleNoELB.png)

A principal diferença para um Grupo de Disponibilidade em Máquinas Virtuais Azure é que estas máquinas virtuais (VMs) requerem um [equilibrador de carga](../../../load-balancer/load-balancer-overview.md). O equilibrador de carga contém os endereços IP para o ouvinte do grupo de disponibilidade. Se tiver mais de um grupo de disponibilidade, cada grupo requer um ouvinte. Um equilibrador de carga pode suportar vários ouvintes.

Além disso, num cluster de failover de hóspedes Azure IaaS VM, recomendamos um único NIC por servidor (nó de cluster) e uma única sub-rede. O azure networking tem redundância física, o que torna NICs adicionais e sub-redes desnecessários em um cluster de hóspedes Azure IaaS VM. Embora o relatório de validação do cluster emita um aviso de que os nós apenas são acessíveis numa única rede, este aviso pode ser ignorado com segurança nos clusters de ativação pós-falha convidados da VM IaaS do Azure. 

Para aumentar a redundância e a elevada disponibilidade, os VMs do SQL Server devem estar no mesmo [conjunto de disponibilidade,](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets)ou em [diferentes zonas de disponibilidade.](/azure/availability-zones/az-overview) 

|  | Versão do Servidor do Windows | Versão do servidor SQL | Edição do Servidor SQL | WSFC Quorum Config | DR com Multi-região | Suporte multi-sub-redes | Apoio a um AD existente | DR com a mesma região de várias zonas | Suporte Dist-AG sem domínio AD | Suporte Dist-AG sem cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| **[Portal do Azure](availability-group-azure-portal-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | Ent | Testemunha de cloud | Não | Sim | Sim | Sim | Não | Não |
| **[Azure CLI / PowerShell](availability-group-az-cli-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | Ent | Testemunha de cloud | Não | Sim | Sim | Sim | Não | Não |
| **[Modelos de arranque rápido](availability-group-quickstart-template-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016  | Ent | Testemunha de cloud | Não | Sim | Sim | Sim | Não | Não |
| **[Manual](availability-group-manually-configure-prerequisites-tutorial.md)** | Todos | Todos | Todos | Todos | Sim | Sim | Sim | Sim | Sim | Sim |

O modelo **SQL Server AlwaysOn Cluster (pré-visualização)** foi removido do Mercado Azure e já não está disponível. 

Quando estiver pronto para construir um grupo de disponibilidade de servidor SQL em Azure Virtual Machines, consulte estes tutoriais.

## <a name="manually-with-azure-cli"></a>Manualmente com Azure CLI

Recomenda-se usar o Azure CLI para configurar e implementar um grupo de disponibilidade porque é o mais simples e rápido de implantar. Com o Azure CLI, a criação do Windows Failover Cluster, juntando VMs do SQL Server ao cluster, bem como a criação do ouvinte e do Balançador de Carga Interna podem ser alcançados em menos de 30 minutos. Esta opção ainda requer uma criação manual do grupo de disponibilidade, mas automatiza todas as outras etapas de configuração necessárias. 

Para obter mais informações, consulte [use Azure SQL VM CLI para configurar sempre no grupo de disponibilidade para O SQL Server num VM Azure](availability-group-az-cli-configure.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente com modelos de arranque rápido Azure

Os Modelos Azure Quickstart utilizam o fornecedor de recursos SQL VM para implantar o Windows Failover Cluster, juntar VMs do SQL Server para ele, criar o ouvinte e configurar o Balançador de Carga Interna. Esta opção requer ainda uma criação manual do grupo de disponibilidade e do Balançador de Carga Interna (ILB). No entanto, automatiza e simplifica todas as outras etapas de configuração necessárias, incluindo a configuração do ILB. 

Para obter mais informações, consulte [use o modelo de arranque rápido Azure para configurar sempre no grupo de disponibilidade para O Servidor SQL num VM Azure](availability-group-quickstart-template-configure.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente com um modelo de portal Azure

[Configure sempre no grupo de disponibilidade em Azure VM automaticamente - Gestor de Recursos](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-the-azure-portal"></a>Manualmente no portal Azure

Também pode criar as máquinas virtuais sem o modelo. Primeiro, complete os pré-requisitos, depois crie o grupo de disponibilidade. Consulte os seguintes tópicos: 

- [Configurar pré-requisitos para o SQL Server Sempre em grupos de disponibilidade em Máquinas Virtuais Azure](availability-group-manually-configure-prerequisites-tutorial.md)

- [Criar Grupo De Disponibilidade Sempre Para Melhorar a disponibilidade e recuperação de desastres](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Passos seguintes

[Configure um servidor SQL sempre no grupo de disponibilidade em máquinas virtuais Azure em diferentes regiões](availability-group-manually-configure-multiple-regions.md)
