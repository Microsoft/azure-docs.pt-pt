---
title: Visão geral dos grupos de disponibilidade
description: Este artigo apresenta SQL Server grupos de disponibilidade em máquinas virtuais do Azure.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037486"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Apresentando SQL Server grupos de disponibilidade em máquinas virtuais do Azure

Este artigo apresenta SQL Server grupos de disponibilidade em máquinas virtuais do Azure. 

Grupos de disponibilidade Always On em máquinas virtuais do Azure são semelhantes a grupos de disponibilidade Always On local. Para obter mais informações, consulte [Always on grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

O diagrama ilustra as partes de um grupo de disponibilidade SQL Server completo em máquinas virtuais do Azure.

![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A principal diferença para um grupo de disponibilidade em máquinas virtuais do Azure é que as máquinas virtuais do Azure exigem um [balanceador de carga](../../../load-balancer/load-balancer-overview.md). O balanceador de carga mantém os endereços IP para o ouvinte do grupo de disponibilidade. Se você tiver mais de um grupo de disponibilidade, cada grupo exigirá um ouvinte. Um balanceador de carga pode dar suporte a vários ouvintes.

Além disso, em um cluster de failover convidado da VM IaaS do Azure, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. As redes do Azure têm redundância físicas, as quais tornam desnecessários NICs e sub-redes adicionais num cluster convidado da VM IaaS do Azure. Embora o relatório de validação do cluster emita um aviso de que os nós apenas são acessíveis numa única rede, este aviso pode ser ignorado com segurança nos clusters de ativação pós-falha convidados da VM IaaS do Azure. 

Para aumentar a redundância e a alta disponibilidade, as VMs SQL Server devem estar no mesmo [conjunto de disponibilidade](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)ou em diferentes [zonas de disponibilidade](/azure/availability-zones/az-overview). 

|  | Versão do Windows Server | Versão do SQL Server | SQL Server Edition | Configuração de quorum do WSFC | DR com várias regiões | Suporte a várias sub-redes | Suporte para um AD existente | DR com a mesma região de várias zonas | Suporte do dist-AG sem domínio do AD | Suporte do dist-AG sem cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [CLI DA VM DO SQL](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Enviado | Testemunha de cloud | Não | Sim | Sim | Sim | Não | Não |
| [Modelos de início rápido](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Enviado | Testemunha de cloud | Não | Sim | Sim | Sim | Não | Não |
| [Modelo de portal](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Enviado | Partilha de ficheiros | Não | Não | Não | Não | Não | Não |
| [Manual](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Todos | Todos | Todos | Todos | Sim | Sim | Sim | Sim | Sim | Sim |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Quando você estiver pronto para criar um grupo de disponibilidade SQL Server em máquinas virtuais do Azure, consulte estes tutoriais.

## <a name="manually-with-azure-cli"></a>Manualmente com CLI do Azure
Usar CLI do Azure para configurar e implantar um grupo de disponibilidade é a opção recomendada, pois é o melhor em termos de simplicidade e velocidade de implantação. Com o CLI do Azure, a criação do cluster de failover do Windows, unindo SQL Server VMs ao cluster, bem como a criação do ouvinte e do Load Balancer interno, pode ser obtida em menos de 30 minutos. Essa opção ainda requer uma criação manual do grupo de disponibilidade, mas automatiza todas as outras etapas de configuração necessárias. 

Para obter mais informações, consulte [usar a CLI de VM do SQL do Azure para configurar Always on grupo de disponibilidade para SQL Server em uma VM do Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente com os modelos de início rápido do Azure
Os modelos de início rápido do Azure utilizam o provedor de recursos da VM do SQL para implantar o cluster de failover do Windows, ingressar SQL Server VMs nele, criar o ouvinte e configurar o Load Balancer interno. Essa opção ainda requer uma criação manual do grupo de disponibilidade e Load Balancer interno (ILB), mas automatiza e simplifica todas as outras etapas de configuração necessárias (incluindo a configuração do ILB). 

Para obter mais informações, consulte [usar o modelo de início rápido do Azure para configurar Always on grupo de disponibilidade para SQL Server em uma VM do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente com um modelo de portal do Azure

[Configurar Always On grupo de disponibilidade na VM do Azure automaticamente-Gerenciador de recursos](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manualmente no portal do Azure

Você também pode criar as máquinas virtuais por conta própria sem o modelo. Primeiro, conclua os pré-requisitos e, em seguida, crie o grupo de disponibilidade. Consulte os seguintes tópicos: 

- [Configurar os pré-requisitos para SQL Server grupos de disponibilidade Always On em máquinas virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Criar Always On grupo de disponibilidade para melhorar a disponibilidade e a recuperação de desastre](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Passos seguintes

[Configurar um grupo de disponibilidade SQL Server Always On em máquinas virtuais do Azure em regiões diferentes](virtual-machines-windows-portal-sql-availability-group-dr.md)
