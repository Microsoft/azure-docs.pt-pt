---
title: Mover o Azure MS para outra região com Azure Site Recovery
description: Use Azure Site Recovery para mover VMs IaaS do Azure de uma região do Azure para outra.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: e3a3db66b4833a8ba21dc9d3c1938f645919221c
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954175"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

O Azure cresce junto com a base de clientes e adiciona suporte a novas regiões para manter o ritmo das demandas crescentes. Novos recursos também são adicionados mensalmente entre serviços. Talvez você queira mover suas VMs (máquinas virtuais) para uma região diferente ou para Zonas de Disponibilidade para aumentar a disponibilidade.

Este tutorial descreve diferentes cenários nos quais você gostaria de mover suas VMs. Ele também descreve como configurar a arquitetura na região de destino para obter maior disponibilidade. 

Neste tutorial, vai aprender o seguinte:

> [!div class="checklist"]
> 
> * Motivos para mover as VMs
> * Arquiteturas típicas
> * Movendo VMs como se encontra para uma região de destino
> * Movendo VMs para aumentar a disponibilidade

## <a name="reasons-to-move-azure-vms"></a>Motivos para mover as VMs do Azure

Você pode mover VMs pelos seguintes motivos:

- Você já implantou em uma região e um novo suporte de região foi adicionado, o que está mais próximo dos usuários finais do seu aplicativo ou serviço. Nesse cenário, você desejaria mover suas VMs como se fosse para a nova região para reduzir a latência. Use a mesma abordagem se você quiser consolidar assinaturas ou se houver regras de governança ou organização que exigem a movimentação.
- Sua VM foi implantada como uma VM de instância única ou como parte de um conjunto de disponibilidade. Se você quiser aumentar os SLAs de disponibilidade, poderá mover suas VMs para uma zona de disponibilidade.

## <a name="steps-to-move-azure-vms"></a>Etapas para mover as VMs do Azure

A movimentação de VMs envolve as seguintes etapas:

1. Verificar os pré-requisitos.
2. Prepare as VMs de origem.
3. Prepare a região de destino.
4. Copie os dados para a região de destino. Use Azure Site Recovery tecnologia de replicação para copiar dados da VM de origem para a região de destino.
5. Teste a configuração. Depois que a replicação for concluída, teste a configuração executando um failover de teste para uma rede que não seja de produção.
6. Execute a movimentação.
7. Descarte os recursos na região de origem.

> [!NOTE]
> Detalhes sobre essas etapas são fornecidos nas seções a seguir.
> [!IMPORTANT]
> Atualmente, o Azure Site Recovery dá suporte à movimentação de VMs de uma região para outra, mas não dá suporte à movimentação em uma região.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Arquiteturas típicas para uma implantação de várias camadas

Esta seção descreve as arquiteturas de implantação mais comuns para um aplicativo de várias camadas no Azure. O exemplo é um aplicativo de três camadas com um IP público. Cada uma das camadas (Web, aplicativo e banco de dados) tem duas VMs cada e está conectada por um balanceador de carga do Azure para outras camadas. A camada de banco de dados tem SQL Server Always On replicação entre as VMs para alta disponibilidade.

* **VMs de instância única implantadas em várias camadas**: cada VM em uma camada é configurada como uma VM de instância única e é conectada por balanceadores de carga a outras camadas. Essa configuração é a mais simples de adotar.

     ![Implantação de VM de instância única entre camadas](media/move-vm-overview/regular-deployment.png)

* **VMs em cada camada implantadas em conjuntos de disponibilidade**: cada VM em uma camada é configurada em um conjunto de disponibilidade. Os [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) garantem que as VMs implantadas no Azure sejam distribuídas entre vários nós de hardware isolados em um cluster. Isso garante que, se ocorrer uma falha de hardware ou de software no Azure, apenas um subconjunto de suas VMs será afetado e sua solução geral permanecerá disponível e operacional.

     ![Implantação de VM entre conjuntos de disponibilidade](media/move-vm-overview/avset.png)

* **VMs em cada camada implantadas em zonas de disponibilidade**: cada VM em uma camada é configurada em [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Uma zona de disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se você criar três ou mais VMs em três zonas em uma região do Azure, suas VMs serão efetivamente distribuídas entre três domínios de falha e três domínios de atualização. A plataforma Azure reconhece essa distribuição entre domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

     ![Implantação de zona de disponibilidade](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Mover VMs como estão para uma região de destino

Com base nas [arquiteturas](#typical-architectures-for-a-multi-tier-deployment) mencionadas anteriormente, veja como será a aparência das implantações depois de executar a movimentação como está para a região de destino.

* **VMs de instância única implantadas em várias camadas**

     ![Implantação de VM de instância única entre camadas](media/move-vm-overview/single-zone.png)

* **VMs em cada camada implantadas em conjuntos de disponibilidade**

     ![Conjuntos de disponibilidade entre regiões](media/move-vm-overview/crossregionaset.png)

* **VMs em cada camada implantadas em Zonas de Disponibilidade**

     ![Implantação de VM em Zonas de Disponibilidade](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Mover VMs para aumentar a disponibilidade

* **VMs de instância única implantadas em várias camadas**

     ![Implantação de VM de instância única entre camadas](media/move-vm-overview/single-zone.png)

* **VMs em cada camada implantadas em conjuntos de disponibilidade**: você pode configurar suas VMs em um conjunto de disponibilidade em zonas de disponibilidade separadas ao habilitar a replicação para sua VM usando Azure site Recovery. O SLA para disponibilidade será de 99,9% depois que você concluir a operação de movimentação.

     ![Implantação de VM entre conjuntos de disponibilidade e Zonas de Disponibilidade](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> 
> * [Mover VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)
> 
> * [Mover VMs do Azure para Zonas de Disponibilidade](move-azure-vms-avset-azone.md)

