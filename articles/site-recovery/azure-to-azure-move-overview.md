---
title: Movendo VMs Azure para outra região com recuperação do site Azure
description: Usando a Recuperação do Sítio Azure para mover VMs Azure de uma região de Azure para outra.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 3f715af835df6783ae5d59dd073a042a553fba4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498041"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Movendo VMs Azure para outra região de Azure

Este artigo fornece uma visão geral das razões e passos envolvidos na deslocação de VMs Azure para outra região de Azure utilizando a Recuperação do [Sítio Azure.](site-recovery-overview.md) 


## <a name="reasons-to-move-azure-vms"></a>Razões para mover VMs Azure

Pode mover VMs pelas seguintes razões:

- Já foi implantado numa região, e foi adicionado um novo apoio à região que está mais próximo dos utilizadores finais da sua aplicação ou serviço. Neste cenário, você gostaria de mover os seus VMs como é para a nova região para reduzir a latência. Use a mesma abordagem se quiser consolidar subscrições ou se houver regras de governação ou organização que o exijam para se mover.
- O seu VM foi implantado como um VM de instância única ou como parte de um conjunto de disponibilidade. Se quiser aumentar a disponibilidade de SLAs, pode mover os seus VMs para uma Zona de Disponibilidade.

## <a name="steps-to-move-azure-vms"></a>Passos para mover VMs Azure

Os VMs em movimento envolvem os seguintes passos:

1. Verificar os pré-requisitos.
2. Prepare m.V.s de origem.
3. Prepare a região alvo.
4. Copiar dados para a região alvo. Utilize a tecnologia de replicação de recuperação de sites Azure para copiar dados da fonte VM para a região alvo.
5. Teste a configuração. Depois de concluída a replicação, teste a configuração executando uma falha de teste numa rede de não produção.
6. Execute o movimento.
7. Descartar os recursos na região nascente.

> [!NOTE]
> Os detalhes sobre estes passos são fornecidos nas seguintes secções.
> [!IMPORTANT]
> Atualmente, a Azure Site Recovery apoia a deslocação de VMs de uma região para outra, mas não suporta a deslocação dentro de uma região.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Arquiteturas típicas para uma implantação de vários níveis

Esta secção descreve as arquiteturas de implantação mais comuns para uma aplicação de vários níveis em Azure. O exemplo é uma aplicação de três camadas com um IP público. Cada um dos níveis (web, aplicação e base de dados) tem dois VMs cada, e são ligados por um equilibrador de carga Azure aos outros níveis. O nível de base de dados tem o SQL Server Always On replicação entre os VMs para alta disponibilidade.

* **VMs de instância única implantados em vários níveis:** Cada VM num nível é configurado como um VM de instância única e é ligado por equilibradores de carga para os outros níveis. Esta configuração é a mais simples de adotar.

     ![Implantação vm de instância única em níveis](media/move-vm-overview/regular-deployment.png)

* **VMs em cada nível implantados em conjuntos**de disponibilidade : Cada VM num nível é configurado num conjunto de disponibilidade. [Os conjuntos](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) de disponibilidade garantem que os VMs que implementa no Azure são distribuídos por vários nós de hardware isolados num cluster. Isto garante que se uma falha de hardware ou software dentro do Azure ocorrer, apenas um subconjunto dos seus VMs é afetado, e a sua solução global permanece disponível e operacional.

     ![Implantação vm em conjuntos de disponibilidade](media/move-vm-overview/avset.png)

* **VMs em cada nível implantadoem em zonas**de disponibilidade : Cada VM num nível é configurado em todas as [Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Uma Zona de Disponibilidade numa região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se criar três ou mais VMs em três zonas de uma região do Azure, os seus VMs são efetivamente distribuídos por três domínios de falha e três domínios de atualização. A plataforma Azure reconhece esta distribuição através de domínios de atualização para garantir que os VMs em diferentes zonas não são atualizados ao mesmo tempo.

     ![Implantação da Zona de Disponibilidade](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Mover VMs como é para uma região alvo

Com base nas [arquiteturas mencionadas](#typical-architectures-for-a-multi-tier-deployment) anteriormente, eis como serão as implementações depois de realizar o movimento como é para a região alvo.

* **VMs de instância única implantados em vários níveis**

     ![Implantação vm de instância única em níveis](media/move-vm-overview/single-zone.png)

* **VMs em cada nível implantados em conjuntos de disponibilidade**

     ![Conjuntos de disponibilidade de regiões cruzadas](media/move-vm-overview/crossregionaset.png)

* **VMs em cada nível implantados em zonas de disponibilidade**

     ![Implantação vM em zonas de disponibilidade](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Mover VMs para aumentar a disponibilidade

* **VMs de instância única implantados em vários níveis**

     ![Implantação vm de instância única em níveis](media/move-vm-overview/single-zone.png)

* **VMs em cada nível implantados em conjuntos**de disponibilidade : Pode configurar os seus VMs numa disponibilidade definida em Zonas de Disponibilidade separadas quando ativa a replicação para o seu VM utilizando a Recuperação do Site Azure. O SLA para disponibilidade será de 99,99% após completar a operação de mudança.

     ![Implantação vM através de conjuntos de disponibilidade e zonas de disponibilidade](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> 
> * [Mover VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)
> 
> * [Mover VMs do Azure para Zonas de Disponibilidade](move-azure-vms-avset-azone.md)

