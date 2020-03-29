---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748856"
---
Este artigo fornece-lhe uma visão geral das funcionalidades de disponibilidade das máquinas virtuais Azure (VMs).

## <a name="high-availability"></a>Elevada disponibilidade

As cargas de trabalho são normalmente espalhadas por diferentes máquinas virtuais para obter alta produção, desempenho e para criar redundância no caso de um VM ser impactado devido a uma atualização ou outro evento. 

Existem poucas opções que o Azure fornece para alcançar a Alta Disponibilidade. Primeiro, falemos de construções básicas. 

### <a name="availability-zones"></a>Zonas de disponibilidade

[As zonas](../articles/availability-zones/az-overview.md) de disponibilidade expandem o nível de controlo que tem para manter a disponibilidade das aplicações e dados nos seus VMs. Uma Zona de Disponibilidade é uma zona fisicamente separada, dentro de uma região de Azure. Existem três Zonas de Disponibilidade por região de Azure apoiada. 

Cada Zona de Disponibilidade tem uma fonte de energia, uma rede e um sistema de refrigeração distintos. Ao arquitetar as suas soluções para utilizar VMs replicados em zonas, pode proteger as suas aplicações e dados da perda de um datacenter. Se uma zona estiver comprometida, as aplicações e os dados replicados estão instantaneamente disponíveis noutra zona. 

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre a implementação de um [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM numa Zona de Disponibilidade.


### <a name="fault-domains"></a>Domínios de falha

Um domínio de falha é um grupo lógico de hardware subjacente que partilha a mesma origem de energia e o mesmo comutador de rede física, semelhante a uma estrutura dentro de um datacenter no local. 

### <a name="update-domains"></a>Domínios de atualização

Um domínio de atualização é um grupo lógico de hardware subjacente que pode entrar em manutenção ou ser reiniciado ao mesmo tempo. 

Esta abordagem garante que, pelo menos, uma instância da aplicação permanece sempre em execução quando a plataforma Azure entra em manutenção periódica. A ordem dos domínios de atualização que estão a ser reiniciados pode não proceder sequencialmente durante a manutenção, mas apenas um domínio de atualização é reiniciado de cada vez.


## <a name="virtual-machines-scale-sets"></a>Conjuntos de escala de máquinas virtuais 

Os conjuntos de escala de máquinas virtuais Azure permitem criar e gerir um grupo de VMs equilibrados de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente como resposta à procura ou a uma agenda definida. Os conjuntos de escala proporcionam uma elevada disponibilidade para as suas aplicações e permitem-lhe gerir, configurar e atualizar centralmente muitos VMs. Recomendamos que dois ou mais VMs sejam criados dentro de uma escala definida para fornecer uma aplicação altamente disponível e para atender o [Azure SLA de 99,95%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Não há qualquer custo para a escala definida em si, você só paga por cada instância VM que cria. Quando um único VM está a utilizar [SSDs premium Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)o Azure SLA aplica-se a eventos de manutenção não planeados. As máquinas virtuais num conjunto de escala podem ser implementadas em vários domínios de atualização e domínios de falha para maximizar a disponibilidade e resiliência a interrupções devido a falhas nos centros de dados, e eventos de manutenção planeados ou não planeados. As máquinas virtuais num conjunto de escala também podem ser implantadas numa única zona de disponibilidade, ou regionalmente. As opções de implementação da zona de disponibilidade podem diferir com base no modo de orquestração.

### <a name="preview-orchestration-mode-preview"></a>Pré-visualização: Antevisão do modo de orquestração
Os conjuntos de escala de máquinas virtuais permitem especificar o modo de orquestração.  Com o modo de orquestração de conjunto de máquinas virtuais (pré-visualização), pode agora escolher se o conjunto de escala deve orquestrar máquinas virtuais que são criadas explicitamente fora de um modelo de configuração de conjunto de escala, ou casos de máquinas virtuais criados implicitamente com base no modelo de configuração. Escolha o modo de orquestração que o modelo de orquestração VM permite agrupar máquinas virtuais explicitamente definidas numa região ou numa zona de disponibilidade. As máquinas virtuais implantadas numa Zona de Disponibilidade proporcionam isolamento zonal aos VMs, estão ligadas ao limite da zona de disponibilidade e não estão sujeitas a quaisquer falhas que possam ocorrer noutrazona de disponibilidade da região. 

|   | "OrchestrationMode": "VM" (VirtualMachine)| "modo de orquestração": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Modelo de configuração VM| Nenhum. O VirtualMachineProfile está indefinido no modelo de conjunto de escala. | Necessário. O VirtualMachineProfile é povoado no modelo de conjunto de escala. |
| Adicionar novo VM ao conjunto de escala| Os VMs são explicitamente adicionados à escala definida quando o VM é criado. | Os VMs são implicitamente criados e adicionados à escala definida com base no modelo de configuração VM, contagem de instâncias e regras de autoscalcificação. |
| Zonas de Disponibilidade| Apoia a implantação regional ou VMs numa zona de disponibilidade| Apoia a implantação regional ou múltiplas Zonas de Disponibilidade; Pode definir a estratégia de equilíbrio da zona |
| Domínios de falha| Pode definir a contagem de domínios de falha. 2 ou 3 com base no apoio regional e 5 na zona de disponibilidade. O domínio de falha VM atribuído persistirá com o ciclo de vida vM, incluindo desalocar e reiniciar. | Pode definir 1, 2 ou 3 domínios de falha para implementações não zonais e 5 para implementações da zona de disponibilidade. O domínio de falha VM atribuído não persiste com o ciclo de vida vm, as máquinas virtuais são atribuídas a um domínio de falha no momento da atribuição. |
| Domínios de atualização| N/D. Os domínios de atualização são automaticamente mapeados para domínios de falha| N/D. Os domínios de atualização são automaticamente mapeados para domínios de falha |

**Domínios de falha e domínios de atualização**

Os conjuntos de escala de máquinas virtuais simplificam o design para alta disponibilidade, alinhando domínios de falhas e atualizando domínios. Só terá de definir que os domínios de falha contam para o conjunto de escala. O número de domínios de avaria disponíveis para os conjuntos de escala pode variar por região. Ver [Gerir a disponibilidade de máquinas virtuais em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).


## <a name="availability-sets"></a>Conjuntos de disponibilidade
Um conjunto de disponibilidade é um agrupamento lógico de VMs dentro de um datacenter que permite ao Azure entender como a sua aplicação é construída para fornecer redundância e disponibilidade. Recomendamos que dois ou mais VMs sejam criados dentro de um conjunto de disponibilidade para fornecer uma aplicação altamente disponível e para atender o [Azure SLA de 99,95%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Não há custo para o Conjunto de Disponibilidade em si, você só paga por cada instância VM que cria. Quando um único VM está a utilizar [SSDs premium Azure,](../articles/virtual-machines/windows/disks-types.md#premium-ssd)o Azure SLA aplica-se a eventos de manutenção não planeados.

Num conjunto de disponibilidade, os VMs são distribuídos automaticamente por estes domínios de falha. Esta abordagem limita o impacto de potenciais falhas de hardware físico, indisponibilidade de rede ou falhas de energia.

Para as VMs que utilizam os [Managed Disks do Azure](../articles/virtual-machines/windows/faq-for-disks.md), as VMs são alinhadas com domínios de falha de discos geridos ao utilizar um conjunto de disponibilidade gerido. Este alinhamento garante que todos os discos geridos ligados a uma VM estão dentro do mesmo domínio de falha do disco gerido. 

Apenas as VMs com discos geridos podem ser criadas num conjunto de disponibilidade gerido. O número de domínios de falha do disco gerido varia por região - dois ou três domínios de falha do disco gerido por região. Pode ler mais sobre estes domínios de falha de disco geridos para [VMs Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) ou [VMs Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Conjunto de disponibilidade gerida](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Os VMs dentro de um conjunto de disponibilidade também são distribuídos automaticamente pelos domínios da atualização. 

![Conjuntos de disponibilidade](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Passos seguintes
Pode agora começar a utilizar estas funcionalidades de redundância e disponibilidade para criar o seu ambiente do Azure. Para informações relativas a melhores práticas, veja [Melhores Práticas de Disponibilidade do Azure](/azure/architecture/checklist/resiliency-per-service).

