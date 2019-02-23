---
title: Mover VMs de IaaS do Azure para outra região do Azure com o serviço Azure Site Recovery | Documentos da Microsoft
description: Utilize o Azure Site Recovery para mover VMs de IaaS do Azure de uma região do Azure para outra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: a73eac1dea731bbf1ffb903ddf2438e791fec9d5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726454"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

O Azure está a crescer extensivamente juntamente com o cliente base e está a adicionar suporte para novas regiões com necessidades de aumento. Também são mais recentes capacidades adicionadas mensalmente em todos os serviços. Por isso, há ocasiões em que quando iria querer mover as suas VMs para uma região diferente ou em zonas de disponibilidade para aumentar a disponibilidade.

Este documento descreve os vários cenários em que iria querer mover as suas VMs e um guia sobre como a arquitetura deve ser configurada no destino para alcançar a elevada disponibilidade. 
> [!div class="checklist"]
> * [Por que seria mover VMs do Azure](#why-would-you-move-azure-vms)
> * [Como mover VMs do Azure](#how-to-move-azure-vms)
> * [Arquiteturas típicas](#typical-architectures-for-a-multi-tier-deployment)
> * [Mover VMs como está para uma região de destino](#move-azure-vms-to-another-region)
> * [Mover VMs para aumentar a disponibilidade](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>Por que seria mover VMs do Azure

Os clientes mover as VMs pelos seguintes motivos:-

- Se já havia implantado numa região e uma nova região foi adicionado suporte, que é mais próxima para os utilizadores finais de serviço ou aplicação, em seguida, iria querer **mover as suas VMs, tal como está, para a nova região** para reduzir a latência. A mesma abordagem é efetuada se de que pretende consolidar subscrições ou existem governação / regras de organização que requeira a transferência. 
- Se a sua VM foi implementada como uma única instância VM ou como parte de disponibilidade do conjunto e pretende aumentar a disponibilidade SLAs, pode **mover as suas VMs para uma zona de disponibilidade**. 

## <a name="how-to-move-azure-vms"></a>Como mover VMs do Azure
Mover VMs envolve os seguintes passos:

1. Verificar os pré-requisitos 
2. Preparar as VMs de origem 
3. Preparar a região de destino 
4. Copiar dados para a região de destino - tecnologia de replicação de utilização do Azure Site Recovery para copiar dados de VM de origem para a região de destino
5. Teste a configuração: Um a replicação estiver concluído, testar a configuração através de uma ativação pós-falha de teste durante a uma rede de não produção.
6. Efetuar a mudança 
7. Eliminar os recursos na região de origem 


> [!IMPORTANT]
> Atualmente o Azure Site Recovery suporta mover VMs a partir de na região para outra e não suporta a movimentação dentro de uma região. 

> [!NOTE]
> Documentação de orientação detalhada sobre estes passos são fornecidos na documentação para cada cenário de como foi mencionado [aqui](#next-steps)

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Arquiteturas típicas para uma implementação de várias camada
Abaixo de secção adotam movimentações por meio dos clientes de arquiteturas de implementação mais comuns, para uma aplicação multicamada no Azure. O exemplo que estamos a tomar aqui é de um aplicativo em camadas três com um IP público. Cada uma das camadas – Web, aplicação e base de dados tem 2 VMs e estão ligadas através de um balanceador de carga para as outras camadas. O escalão de base de dados tem a replicação do SQL Always ON entre as VMs de elevada disponibilidade (HA).

1.  **Instâncias de VMs implementadas em vários escalões de único**-cada VM numa camada está configurada como uma única instância de VM, ligada por balanceadores de carga para as outras camadas. Esta é a configuração mais simples que os clientes adotam.

       ![VMs de único](media/move-vm-overview/regular-deployment.PNG)

2. **VMs em cada camada implementadas em conjunto de disponibilidade** -cada VM numa camada está configurada num disponibilidade definida. [Conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) garantem que as VMs que implementa no Azure são distribuídas por vários nós de hardware isolados num cluster. Fazer isto garante que, se ocorrer uma falha de hardware ou software no Azure, apenas um subconjunto das suas VMs é afetado e que a solução global permanece disponível e operacional. 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **VMs em cada camada implementadas em conjunto de disponibilidade** -cada VM numa camada está configurada entre [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Uma zona de disponibilidade na região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se criar três ou mais VMs por zonas de três numa região do Azure, as suas VMs com eficiência são distribuídas entre três domínios de falha e três domínios de atualização. A plataforma do Azure reconhece essa distribuição em vários domínios de atualização para se certificar de que as VMs em diferentes horários não são atualizadas ao mesmo tempo.

      ![zone-deploymnt](media/move-vm-overview/zone.PNG)



## <a name="move-vms-as-is-to-a-target-region"></a>Mover VMs como está para uma região de destino

Com base em acima mencionado [arquiteturas](#typical-architectures-for-a-multi-tier-deployment), aqui está como as implementações ficarão como uma vez que executar a migração, pois é a região de destino.


1. **As VMs de instância única implementado em vários escalões** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **VMs em cada camada implementadas em conjunto de disponibilidade**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **VMs em cada camada implementados através de zona de disponibilidade**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>Mover VMs para aumentar a disponibilidade

1. **As VMs de instância única implementado em vários escalões** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **VMs em cada camada implementadas em conjunto de disponibilidade** -pode optar por configurar para colocar as VMs num conjunto em zonas de disponibilidade separadas, quando opta por ativar a replicação para a sua VM com o Azure Site Recovery de disponibilidade. Depois de concluir a operação de movimentação, o SLA de disponibilidade será 99,9%.

     ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>Passos Seguintes

Neste documento, leia sobre as diretrizes gerais para mover VMs. Para saber a execução do passo a passo para isso, leia mais:


> [!div class="nextstepaction"]
> * [Mover as VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)

> * [Mover VMs do Azure para zonas de disponibilidade](move-azure-VMs-AVset-Azone.md)

