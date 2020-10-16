---
title: Mover VMs Azure para outra região com recuperação do local de Azure
description: Utilizando a recuperação do local de Azure para mover VMs Azure de uma região de Azure para outra.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 61d596c4b3a65c54e1a70682adad5b7328c384f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90007371"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Mover VMs do Azure para outra região do Azure

Este artigo fornece uma visão geral das razões e passos envolvidos na deslocação dos VMs Azure para outra região de Azure utilizando [a recuperação do sítio Azure](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Razões para mover VMs Azure

Pode mover VMs pelas seguintes razões:

- Já foi implantado numa região, tendo sido adicionado um novo apoio regional, que está mais próximo dos utilizadores finais da sua aplicação ou serviço. Neste cenário, você gostaria de mover os seus VMs como é para a nova região para reduzir a latência. Use a mesma abordagem se quiser consolidar subscrições ou se houver regras de governação ou organização que o exijam a mudança.
- O seu VM foi implantado como um VM de uma única instância ou como parte de um conjunto de disponibilidade. Se quiser aumentar os SLAs de disponibilidade, pode mover os seus VMs para uma Zona de Disponibilidade.

## <a name="move-vms-with-resource-mover"></a>Mover VMs com Mudança de Recursos

Agora pode mover VMs para outra região com [Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md). O Resource Mover está em pré-visualização pública e fornece:
- Um único centro para movimentar recursos através das regiões.
- Tempo de movimento reduzido e complexidade. Tudo o que precisa está num único local.
- Uma experiência simples e consistente para mover diferentes tipos de recursos Azure.
- Uma maneira fácil de identificar dependências entre recursos que quer mover. Isto ajuda-o a mover recursos relacionados em conjunto, para que tudo funcione como esperado na região alvo, após a mudança.
- Limpeza automática dos recursos na região de origem, se quiser eliminá-los após a mudança.
- A testar. Podes experimentar um movimento, e depois deitá-lo fora se não quiseres fazer um movimento completo.



## <a name="move-vms-with-site-recovery"></a>Mover VMs com recuperação do local

Mover VMs com recuperação do local envolve os seguintes passos:

1. Verificar os pré-requisitos.
2. Prepare os VMs de origem.
3. Prepare a região alvo.
4. Copie os dados para a região alvo. Utilize a tecnologia de replicação do local de recuperação do Azure para copiar dados da origem VM para a região alvo.
5. Teste a configuração. Após a replicação estar concluída, teste a configuração executando uma falha de teste para uma rede de não produção.
6. Faça o movimento.
7. Deite fora os recursos na região origem.

> [!NOTE]
> Os detalhes sobre estes passos são fornecidos nas seguintes secções.
> [!IMPORTANT]
> Atualmente, a Azure Site Recovery suporta a deslocação de VMs de uma região para outra, mas não suporta mover-se dentro de uma região.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Arquiteturas típicas para uma implantação de vários níveis

Esta secção descreve as arquiteturas de implantação mais comuns para uma aplicação multi-nível em Azure. O exemplo é uma aplicação de três camadas com um IP público. Cada um dos níveis (web, aplicação e base de dados) tem dois VMs cada, e estão ligados por um equilibrador de carga Azure aos outros níveis. O nível de base de dados tem o SQL Server Always On replication entre os VMs para uma elevada disponibilidade.

* **VMs de instância única implantados em vários níveis**: Cada VM num nível é configurado como um VM de instância única e é ligado por equilibradores de carga aos outros níveis. Esta configuração é a mais simples de adotar.

     ![Seleção para mover a implantação de VM de uma só instância através de níveis](media/move-vm-overview/regular-deployment.png)

* **VMs em cada nível implantados em conjuntos de disponibilidade**: Cada VM num nível é configurado num conjunto de disponibilidade. [Os conjuntos de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) garantem que os VMs que implementa no Azure são distribuídos por múltiplos nós de hardware isolados num cluster. Isto garante que se uma falha de hardware ou software dentro do Azure acontecer, apenas um subconjunto dos seus VMs são afetados, e a sua solução global permanece disponível e operacional.

     ![Implementação de VM em conjuntos de disponibilidade](media/move-vm-overview/avset.png)

* **VMs em cada nível implantados através de Zonas de Disponibilidade**: Cada VM num nível é configurado através de [Zonas de Disponibilidade](../availability-zones/az-overview.md). Uma Zona de Disponibilidade numa região de Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se criar três ou mais VMs em três zonas numa região de Azure, os seus VMs são efetivamente distribuídos por três domínios de avaria e três domínios de atualização. A plataforma Azure reconhece esta distribuição através de domínios de atualização para garantir que os VMs em diferentes zonas não sejam atualizados ao mesmo tempo.

     ![Implementação de Zona de Disponibilidade](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Mover VMs como é para uma região alvo

Com base nas [arquiteturas mencionadas](#typical-architectures-for-a-multi-tier-deployment) anteriormente, eis como serão os lançamentos depois de realizar o movimento como é para a região alvo.

* **VMs de instância única implantados em vários níveis**
* **VMs em cada nível implantados em conjuntos de disponibilidade**
* **VMs em cada nível implantados em zonas de disponibilidade**

## <a name="move-vms-to-increase-availability"></a>Mover VMs para aumentar a disponibilidade

* **VMs de instância única implantados em vários níveis**

     ![Implantação de VM de uma só instância em níveis](media/move-vm-overview/single-zone.png)

* **VMs em cada nível implantados em conjuntos de disponibilidade**: Pode configurar os seus VMs numa disponibilidade definida em zonas de disponibilidade separadas quando ativar a replicação para o seu VM utilizando a Recuperação do Site Azure. O SLA para disponibilidade será de 99,99% depois de concluir a operação de movimento.

     ![Implementação de VM em conjuntos de disponibilidade e zonas de disponibilidade](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> 
> * [Mover VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)
> 
> * [Mover VMs Azure em Zonas de Disponibilidade](move-azure-vms-avset-azone.md)

