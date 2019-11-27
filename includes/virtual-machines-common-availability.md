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
ms.openlocfilehash: 06b54c3038e8b4f5879a93b696920534c2199008
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414588"
---
Este artigo fornece uma visão geral dos recursos de disponibilidade das VMs (máquinas virtuais) do Azure.

## <a name="high-availability"></a>Elevada disponibilidade

As cargas de trabalho normalmente se disseminam entre diferentes máquinas virtuais para obter alta taxa de transferência, desempenho e para criar redundância no caso de uma VM ser afetada devido a uma atualização ou outro evento. 

Há algumas opções que o Azure fornece para obter alta disponibilidade. Primeiro, vamos falar sobre construções básicas. 

### <a name="availability-zones"></a>Zonas de disponibilidade

As [zonas de disponibilidade](../articles/availability-zones/az-overview.md) expandem o nível de controle que você precisa para manter a disponibilidade dos aplicativos e dos dados em suas VMs. Uma zona de disponibilidade é uma zona fisicamente separada, dentro de uma região do Azure. Há três Zonas de Disponibilidade por região do Azure com suporte. 

Cada Zona de Disponibilidade tem uma fonte de energia, uma rede e um sistema de refrigeração distintos. Ao arquitetar suas soluções para usar VMs replicadas em zonas, você pode proteger seus aplicativos e dados contra a perda de um datacenter. Se uma zona for comprometida, os aplicativos e dados replicados estarão instantaneamente disponíveis em outra zona. 

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre como implantar uma VM do [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) em uma zona de disponibilidade.


### <a name="fault-domains"></a>Domínios de falha

Um domínio de falha é um grupo lógico de hardware subjacente que partilha a mesma origem de energia e o mesmo comutador de rede física, semelhante a uma estrutura dentro de um datacenter no local. 

### <a name="update-domains"></a>Domínios de atualização

Um domínio de atualização é um grupo lógico de hardware subjacente que pode entrar em manutenção ou ser reiniciado ao mesmo tempo. 

Esta abordagem garante que, pelo menos, uma instância da aplicação permanece sempre em execução quando a plataforma Azure entra em manutenção periódica. A ordem dos domínios de atualização sendo reinicializados pode não continuar sequencialmente durante a manutenção, mas apenas um domínio de atualização é reinicializado por vez.


## <a name="virtual-machines-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais 

Os conjuntos de dimensionamento de máquinas virtuais do Azure permitem criar e gerenciar um grupo de VMs com balanceamento de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente como resposta à procura ou a uma agenda definida. Os conjuntos de dimensionamento fornecem alta disponibilidade para seus aplicativos e permitem que você gerencie, configure e atualize centralmente muitas VMs. Recomendamos que duas ou mais VMs sejam criadas dentro de um conjunto de dimensionamento para fornecer um aplicativo altamente disponível e para atender ao [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Não há nenhum custo para o conjunto de dimensionamento em si, você só paga por cada instância de VM que criar. Quando uma única VM está usando o [SSDs Premium do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), o SLA do Azure se aplica a eventos de manutenção não planejada. As máquinas virtuais em um conjunto de dimensionamento podem ser implantadas em vários domínios de atualização e domínios de falha para maximizar a disponibilidade e a resiliência a interrupções devido a interrupções de data center e eventos de manutenção planejados ou não planejados. As máquinas virtuais em um conjunto de dimensionamento também podem ser implantadas em uma única zona de disponibilidade ou regiões. As opções de implantação de zona de disponibilidade podem ser diferentes com base no modo de orquestração.

### <a name="preview-orchestration-mode-preview"></a>Visualização: visualização do modo de orquestração
Os conjuntos de dimensionamento de máquinas virtuais permitem que você especifique o modo de orquestração.  Com o modo de orquestração do conjunto de dimensionamento de máquinas virtuais (versão prévia), agora você pode escolher se o conjunto de dimensionamento deve orquestrar máquinas virtuais que são criadas explicitamente fora de um modelo de configuração de conjunto de dimensionamento ou instâncias de máquina virtual criadas implicitamente com base no modelo de configuração. Escolha o modo de orquestração que o modelo de orquestração de VM permite agrupar máquinas virtuais explicitamente definidas em uma região ou em uma zona de disponibilidade. As máquinas virtuais implantadas em uma zona de disponibilidade fornecem isolamento zonal para as VMs estão associadas ao limite de zonas de disponibilidade e não estão sujeitas a nenhuma falha que possa ocorrer em outra zona de disponibilidade na região. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Modelo de configuração de VM| Nenhuma. VirtualMachineProfile é indefinido no modelo do conjunto de dimensionamento. | Necessário. VirtualMachineProfile é populado no modelo de conjunto de dimensionamento. |
| Adicionando Nova VM ao conjunto de dimensionamento| As VMs são adicionadas explicitamente ao conjunto de dimensionamento quando a VM é criada. | As VMs são criadas implicitamente e adicionadas ao conjunto de dimensionamento com base no modelo de configuração da VM, contagem de instâncias e regras de dimensionamento automático. |
| Zonas de Disponibilidade| Dá suporte à implantação regional ou a VMs em uma zona de disponibilidade| Dá suporte à implantação regional ou a vários Zonas de Disponibilidade; Pode definir a estratégia de balanceamento de zona |
| Domínios de falha| Pode definir a contagem de domínios de falha. 2 ou 3 com base no suporte regional e 5 para a zona de disponibilidade. O domínio de falha de VM atribuído continuará com o ciclo de vida da VM, incluindo desalocar e reiniciar. | Pode definir 1, 2 ou três domínios de falha para implantações não zonais e 5 para implantações de zona de disponibilidade. O domínio de falha de VM atribuído não persiste com o ciclo de vida da VM, as máquinas virtuais recebem um domínio de falha no momento da alocação. |
| Domínios de atualização| N/D. Os domínios de atualização são mapeados automaticamente para domínios de falha| N/D. Os domínios de atualização são mapeados automaticamente para domínios de falha |

**Domínios de falha e domínios de atualização**

Os conjuntos de dimensionamento de máquinas virtuais simplificam o design para alta disponibilidade alinhando domínios de falha e domínios de atualização. Você precisará definir apenas a contagem de domínios de falha para o conjunto de dimensionamento. O número de domínios de falha disponíveis para os conjuntos de dimensionamento pode variar por região. Consulte [número de domínios de falha por região](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region).


## <a name="availability-sets"></a>Conjuntos de disponibilidade
Um conjunto de disponibilidade é um agrupamento lógico de VMs em um datacenter que permite que o Azure entenda como seu aplicativo foi criado para fornecer redundância e disponibilidade. Recomendamos que duas ou mais VMs sejam criadas dentro de um conjunto de disponibilidade para fornecer um aplicativo altamente disponível e para atender ao [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Não há nenhum custo para o próprio conjunto de disponibilidade, você paga apenas por cada instância de VM que criar. Quando uma única VM está usando o [SSDs Premium do Azure](../articles/virtual-machines/windows/disks-types.md#premium-ssd), o SLA do Azure se aplica a eventos de manutenção não planejada.

Em um conjunto de disponibilidade, as VMs são distribuídas automaticamente entre esses domínios de falha. Esta abordagem limita o impacto de potenciais falhas de hardware físico, indisponibilidade de rede ou falhas de energia.

Para as VMs que utilizam os [Managed Disks do Azure](../articles/virtual-machines/windows/faq-for-disks.md), as VMs são alinhadas com domínios de falha de discos geridos ao utilizar um conjunto de disponibilidade gerido. Este alinhamento garante que todos os discos geridos ligados a uma VM estão dentro do mesmo domínio de falha do disco gerido. 

Apenas as VMs com discos geridos podem ser criadas num conjunto de disponibilidade gerido. O número de domínios de falha do disco gerido varia por região - dois ou três domínios de falha do disco gerido por região. Você pode ler mais sobre esses domínios de falha de disco gerenciado para VMs [Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) ou [VMs do Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Conjunto de disponibilidade gerenciado](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


As VMs em um conjunto de disponibilidade também são distribuídas automaticamente entre domínios de atualização. 

![Conjuntos de disponibilidade](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Passos Seguintes
Pode agora começar a utilizar estas funcionalidades de redundância e disponibilidade para criar o seu ambiente do Azure. Para informações relativas a melhores práticas, veja [Melhores Práticas de Disponibilidade do Azure](/azure/architecture/checklist/resiliency-per-service).

