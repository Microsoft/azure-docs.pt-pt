---
title: Acerca do mapeamento de rede para a recuperação de desastre de VM de Hyper-V (com VMM) para o Azure com o Site Recovery | Documentos da Microsoft
description: Descreve como configurar o mapeamento de rede para recuperação após desastre de VMs de Hyper-V (geridas em clouds do VMM) para o Azure, com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: cefde79cf8c544a6900b1efa5dbcefbc43638d40
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009961"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Preparar o mapeamento de rede para a recuperação de desastres da VM de Hyper-V para o Azure


Este artigo ajuda-o a compreender e preparar o mapeamento da rede ao replicar VMs de Hyper-V em clouds do System Center Virtual Machine Manager (VMM) para o Azure ou para um site secundário, utilizando o [do Azure Site Recovery](site-recovery-overview.md) serviço.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Preparar o mapeamento de rede para replicação no Azure

Quando estiver a replicar para o Azure, mapas de mapeamento de rede entre redes VM num servidor do VMM de origem e as redes virtuais do Azure de destino. O mapeamento faz o seguinte:
-  **Ligação de rede**— garante que as VMs replicadas do Azure estão ligadas à rede mapeada. Todas as máquinas que efetuar a ativação pós-falha na mesma rede podem ligar-se entre si, mesmo a ativação pós-falha nos planos de recuperação diferente.
- **Gateway de rede**— se um gateway de rede está configurado na rede do Azure de destino, as VMs podem ligar a outras máquinas de virtuais no local.

Mapeamento da rede funciona da seguinte forma:

- Mapear uma rede de VM do VMM de origem a uma rede virtual do Azure.
- Após a ativação pós-falha de VMs do Azure na origem de rede será ligada à rede virtual de destino mapeadas.
- Novas VMs adicionadas à rede VM de origem estão ligadas à rede do Azure mapeada quando ocorre a replicação.
- Se a rede de destino tiver várias sub-redes e um dessas sub-redes tiver o mesmo nome que a sub-rede onde está localizada a máquina virtual de origem, a máquina virtual de réplica liga-se a essa sub-rede de destino após a ativação pós-falha.
- Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual liga-se à primeira sub-rede da rede.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Preparar o mapeamento de rede para a replicação para um site secundário

Quando está a replicar para um site secundário, mapas de mapeamento de rede entre redes VM num servidor VMM de origem e as redes VM num servidor do VMM de destino. O mapeamento faz o seguinte:

- **Ligação de rede**— VMs liga-se às redes adequadas após a ativação pós-falha. A VM de réplica será ligada à rede de destino que está mapeada para a rede de origem.
- **Posicionamento da VM ideal**— ideal coloca a VMs de réplica nos servidores de anfitrião do Hyper-V. VMs de réplica são colocadas em anfitriões que podem aceder a redes VM mapeadas.
- **Nenhum mapeamento de rede**— se não configurar o mapeamento da rede, as VMs de réplica não será ligada a quaisquer redes VM após a ativação pós-falha.

Mapeamento da rede funciona da seguinte forma:

- Mapeamento da rede pode ser configurado entre redes VM em dois servidores VMM ou num único servidor VMM se dois sites que são geridos pelo mesmo servidor.
- Quando mapeamento está configurado corretamente e replicação estiver ativada, uma VM na localização principal será ligada a uma rede e sua réplica na localização de destino será ligada à sua rede mapeada.
- Quando seleciona uma rede VM de destino durante o mapeamento de rede na recuperação de sites, as nuvens de origem do VMM que utilizam a rede VM de origem serão apresentadas, juntamente com as redes VM de destino disponíveis nas nuvens de destino que são utilizadas para proteção.
- Se a rede de destino tiver várias sub-redes e um dessas sub-redes tiver o mesmo nome que a sub-rede onde está localizada a máquina virtual de origem, em seguida, a VM de réplica será ligada a essa sub-rede de destino após a ativação pós-falha. Se não houver nenhuma sub-rede de destino com um nome correspondente, a VM será ligada à primeira sub-rede da rede.

## <a name="example"></a>Exemplo

Eis um exemplo para ilustrar esse mecanismo. Vamos dar uma organização com duas localizações em nova York e Chicago.

**Localização** | **Servidor VMM** | **Redes VM** | **Mapeado para**
---|---|---|---
Nova Iorque | VMM-NewYork| VMNetwork1-NewYork | Mapeado para VMNetwork1 Chicago
 |  | VMNetwork2-NewYork | Não mapeada
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapeado para VMNetwork1 NewYork
 | | VMNetwork2-Chicago | Não mapeada

Neste exemplo:

- Quando uma réplica a que VM é criada para qualquer VM que está ligada ao VMNetwork1 NewYork, ele se conectará ao VMNetwork1 Chicago.
- Quando uma réplica a que VM é criada para VMNetwork2 NewYork ou VMNetwork2 Chicago, não será ligada a nenhuma rede.

Eis como nuvens do VMM são configuradas em nossa organização de exemplo e as redes lógicas associadas às nuvens.

### <a name="cloud-protection-settings"></a>Definições de proteção de cloud

**Nuvem protegida** | **Proteção de cloud** | **Rede lógica (Nova Iorque)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Definições de rede lógicas e de VMS

**Localização** | **Rede lógica** | **Rede VM associado**
---|---|---
Nova Iorque | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Definições de rede de destino

Com base nestas definições, ao selecionar a rede de VM de destino, a tabela seguinte mostra as opções que estarão disponíveis.

**Selecionar** | **Nuvem protegida** | **Proteção de cloud** | **Rede de destino disponível**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponível
 | GoldCloud1 | GoldCloud2 | Disponível
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Não disponível
 | GoldCloud1 | GoldCloud2 | Disponível


Se a rede de destino tiver várias sub-redes e um dessas sub-redes tiver o mesmo nome que a sub-rede onde está localizada a máquina virtual de origem, em seguida, a máquina virtual de réplica será ligada a essa sub-rede de destino após a ativação pós-falha. Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será ligada à primeira sub-rede da rede.


### <a name="failback-behavior"></a>Comportamento de reativação pós-falha

Para ver o que acontece no caso de reativação pós-falha (a replicação inversa), vamos supor que VMNetwork1 NewYork é mapeado para VMNetwork1-Chicago, com as seguintes definições.


**VM** | **Ligado à rede VM**
---|---
VM1 | VMNetwork1-Network
VM2 (réplica de VM1) | VMNetwork1-Chicago

Com estas definições, vamos rever o que acontece em alguns cenários possíveis.

**Cenário** | **Resultado**
---|---
Nenhuma alteração nas propriedades de rede de VM-2 após a ativação pós-falha. | 1 da VM permanece ligado à rede de origem.
Propriedades de rede de VM-2 são alterados após a ativação pós-falha e está desligado. | 1 da VM está desligado.
Propriedades de rede de VM-2 sejam alterados após a ativação pós-falha e está ligado ao VMNetwork2 Chicago. | Se não estiver mapeada VMNetwork2 Chicago, 1 da VM será desligado.
Mapeamento da rede de VMNetwork1 Chicago é alterado. | 1 da VM será ligada à rede, agora, mapeada para VMNetwork1 Chicago.



## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre](hyper-v-vmm-networking.md) endereçamento IP após a ativação pós-falha para um site VMM secundário.
- [Saiba mais sobre](concepts-on-premises-to-azure-networking.md) endereçamento IP após a ativação pós-falha do Azure.
