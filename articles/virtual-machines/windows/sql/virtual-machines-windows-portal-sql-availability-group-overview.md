---
title: -Máquinas virtuais do Azure - descrição-geral de grupos de disponibilidade do SQL Server | Documentos da Microsoft
description: Este artigo apresenta os grupos de disponibilidade do SQL Server em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: 728d73ee954a6ddebf80c6a9c466784c9c79bd53
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819161"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Introdução ao SQL Server Always On grupos de disponibilidade em máquinas virtuais do Azure #

Este artigo apresenta os grupos de disponibilidade do SQL Server em máquinas de virtuais do Azure. 

Always On grupos de disponibilidade em máquinas de virtuais do Azure são semelhantes às sempre em grupos de disponibilidade no local. Para obter mais informações, consulte [grupos de Disponibilidade AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

O diagrama ilustra as partes de um grupo de disponibilidade de servidor de SQL completo em máquinas de virtuais do Azure.

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A principal diferença para um grupo de disponibilidade em máquinas de virtuais do Azure é que as máquinas virtuais do Azure, exigir um [Balanceador de carga](../../../load-balancer/load-balancer-overview.md). O Balanceador de carga contém os endereços IP para o serviço de escuta do grupo de disponibilidade. Se tiver mais de um grupo de disponibilidade cada grupo requer um serviço de escuta. Um balanceador de carga pode oferecer suporte a vários serviços de escuta.

Além disso, num cluster de ativação pós-falha de convidado de VM de IaaS do Azure, recomendamos um único NIC por servidor (nó de cluster) e uma única sub-rede. Redes do Azure tem redundância física que torna desnecessário NICs e sub-redes adicionais num cluster de convidados de VM de IaaS do Azure. Embora o relatório de validação de cluster irá emitir um aviso de que os nós só são acessíveis numa única rede, este aviso pode ser ignorado com segurança em clusters de ativação pós-falha de convidado de VM de IaaS do Azure. 

|  | Versão do Windows Server | Versão do SQL Server | SQL Server Edition | Configuração de quórum do WSFC | DR com várias regiões | Suporte com várias sub-redes | Suporte para um AD existente | DR com várias zona mesma região | Suporte de DIST AG com nenhum domínio do AD | Suporte de DIST AG com nenhum cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [CLI DA VM DO SQL](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Testemunho de nuvem | Não | Sim | Sim | Sim | Não | Não |
| [Modelos de início rápido](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Testemunho de nuvem | Não | Sim | Sim | Sim | Não | Não |
| [Modelo do portal](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Partilha de ficheiros | Não | Não | Não | Não | Não | Não |
| [Manual](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Todos | Todos | Todos | Todos | Sim | Sim | Sim | Sim | Sim | Sim |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Quando estiver pronto para criar um grupo de disponibilidade do SQL Server em máquinas de virtuais do Azure, veja estes tutoriais.

## <a name="manually-with-azure-cli"></a>Manualmente com a CLI do Azure
Com a CLI do Azure para configurar e implementar um grupo de disponibilidade é a opção recomendada, pois é o melhor em termos de simplicidade e velocidade de implementação. Com a CLI do Azure, a criação do Cluster de ativação pós-falha do Windows, ingressar em VMs do SQL Server para o cluster, bem como a criação do serviço de escuta e o Balanceador de carga interno podem todas ser realizadas em menos de 30 minutos. Esta opção requer uma criação manual do grupo de disponibilidade ainda, mas automatiza a todos os outros passos de configuração necessárias. 

Para obter mais informações, consulte [utilização CLI na VM de SQL do Azure para configurar o grupo de disponibilidade Always On do SQL Server numa VM do Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente com os modelos de início rápido do Azure
Os modelos de início rápido do Azure utilizam o fornecedor de recursos de VM do SQL Server para implementar o Cluster de ativação pós-falha do Windows, Junte-se a VMs do SQL Server para o mesmo, criar o serviço de escuta e configurar o Balanceador de carga interno. Esta opção ainda requer uma criação manual do grupo de disponibilidade e o Balanceador de carga interno (ILB), mas automatiza e simplifica a todos os outros passos de configuração necessárias (incluindo a configuração do ILB). 

Para obter mais informações, consulte [modelo de início rápido do Azure de utilização para configurar o grupo de disponibilidade Always On do SQL Server numa VM do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente com um modelo do Portal do Azure

[Configurar grupo de disponibilidade Always On na VM do Azure automaticamente - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manualmente no portal do Azure

Também pode criar as máquinas virtuais mesmo sem o modelo. Em primeiro lugar, concluir os pré-requisitos, em seguida, criar o grupo de disponibilidade. Consulte os seguintes tópicos: 

- [Configurar os pré-requisitos para grupos de disponibilidade Always On do SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Criar grupo de Disponibilidade AlwaysOn para melhorar a disponibilidade e recuperação após desastre](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Passos Seguintes

[Configurar um SQL Server AlwaysOn no grupo de disponibilidade em máquinas virtuais do Azure em regiões diferentes](virtual-machines-windows-portal-sql-availability-group-dr.md)
