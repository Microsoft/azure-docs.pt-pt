---
title: Visão geral dos grupos de disponibilidade
description: Este artigo introduz grupos de disponibilidade de servidores SQL em máquinas virtuais Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037486"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Introdução de grupos de disponibilidade do Servidor SQL em máquinas virtuais Azure

Este artigo introduz grupos de disponibilidade do SQL Server em Máquinas Virtuais Azure. 

Sempre em grupos de disponibilidade em Máquinas Virtuais Azure são semelhantes aos sempre em grupos de disponibilidade nas instalações. Para mais informações, consulte [Sempre em Grupos de Disponibilidade (Servidor SQL)](https://msdn.microsoft.com/library/hh510230.aspx). 

O diagrama ilustra as partes de um grupo completo de disponibilidade de servidor EsqL em máquinas virtuais Azure.

![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A diferença fundamental para um Grupo de Disponibilidade em Máquinas Virtuais Azure é que as máquinas virtuais Azure, requerem um [equilíbrio de carga.](../../../load-balancer/load-balancer-overview.md) O equilibrista de carga detém os endereços IP para o ouvinte do grupo de disponibilidade. Se tiver mais de um grupo de disponibilidade, cada grupo requer um ouvinte. Um equilibrador de carga pode suportar vários ouvintes.

Além disso, num cluster de failover de hóspedes Do VM Azure IaaS, recomendamos um único NIC por servidor (nó de cluster) e uma única subnet. As redes do Azure têm redundância físicas, as quais tornam desnecessários NICs e sub-redes adicionais num cluster convidado da VM IaaS do Azure. Embora o relatório de validação do cluster emita um aviso de que os nós apenas são acessíveis numa única rede, este aviso pode ser ignorado com segurança nos clusters de ativação pós-falha convidados da VM IaaS do Azure. 

Para aumentar o despedimento e a elevada disponibilidade, os VMs do Servidor SQL devem estar no mesmo conjunto de [disponibilidade](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets), ou [em diferentes zonas de disponibilidade.](/azure/availability-zones/az-overview) 

|  | Versão do Servidor do Windows | Versão do Servidor SQL | Edição do Servidor SQL | WSFC Quorum Config | DR com Multi-região | Suporte multi-sub-rede | Apoio a um AD existente | DR com região multi-zona mesma | Suporte Dist-AG sem domínio AD | Suporte Dist-AG sem cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Testemunha de cloud | Não | Sim | Sim | Sim | Não | Não |
| [Modelos de arranque rápido](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Testemunha de cloud | Não | Sim | Sim | Sim | Não | Não |
| [Modelo do portal](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Partilha de ficheiros | Não | Não | Não | Não | Não | Não |
| [Manual](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Todos | Todos | Todos | Todos | Sim | Sim | Sim | Sim | Sim | Sim |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Quando estiver pronto para construir um grupo de disponibilidade do SQL Server em Máquinas Virtuais Azure, consulte estes tutoriais.

## <a name="manually-with-azure-cli"></a>Manualmente com O CLI Azure
Usar o Azure CLI para configurar e implementar um grupo de disponibilidade é a opção recomendada, uma vez que é a melhor em termos de simplicidade e rapidez de implantação. Com o Azure CLI, a criação do Cluster Failover do Windows, juntando VMs do Servidor SQL ao cluster, bem como a criação do ouvinte e balancedor de carga interna podem ser alcançados em menos de 30 minutos. Esta opção ainda requer uma criação manual do grupo de disponibilidade, mas automatiza todas as outras etapas de configuração necessárias. 

Para mais informações, consulte [Use Azure SQL VM CLI para configurar sempre em grupo de disponibilidade para O Servidor SQL num VM Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente com modelos de quickstart azure
Os modelos De Quickstart Azure utilizam o fornecedor de recursos SQL VM para implementar o Cluster Failover do Windows, juntar VMs do Servidor SQL, criar o ouvinte e configurar o Balancer de Carga Interna. Esta opção ainda requer uma criação manual do grupo de disponibilidade, e Balancer de Carga Interna (ILB), mas automatiza e simplifica todas as outras etapas de configuração necessárias (incluindo a configuração do ILB). 

Para mais informações, consulte [o modelo De arranque rápido azure para configurar sempre em grupo de disponibilidade para o Servidor SQL num VM Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente com um modelo de portal Azure

[Configure Sempre Em Grupo de Disponibilidade no Azure VM automaticamente - Gestor de Recursos](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manualmente no portal Azure

Você também pode criar as máquinas virtuais sem o modelo. Primeiro, complete os pré-requisitos e, em seguida, crie o grupo de disponibilidade. Consulte os seguintes tópicos: 

- [Configure pré-requisitos para O Servidor SQL Sempre em grupos de disponibilidade em Máquinas Virtuais Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Criar sempre em grupo de disponibilidade para melhorar a disponibilidade e recuperação de desastres](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Passos seguintes

[Configure um servidor SQL sempre em grupo de disponibilidade em máquinas virtuais azure em diferentes regiões](virtual-machines-windows-portal-sql-availability-group-dr.md)
