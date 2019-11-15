---
title: Sobre o mapeamento de rede do Hyper-V (com VMM) com Site Recovery
description: Descreve como configurar o mapeamento de rede para recuperação de desastres de VMs do Hyper-V (gerenciados em nuvens do VMM) para o Azure, com Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082564"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Preparar o mapeamento de rede para a recuperação de desastre de VM do Hyper-V para o Azure


Este artigo ajuda você a entender e preparar-se para o mapeamento de rede ao replicar VMs do Hyper-V em nuvens System Center Virtual Machine Manager (VMM) para o Azure, ou para um site secundário, usando o serviço [Azure site Recovery](site-recovery-overview.md) .


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Preparar o mapeamento de rede para replicação no Azure

Quando você estiver replicando para o Azure, o mapeamento de rede mapeia entre redes VM em um servidor VMM de origem e as redes virtuais do Azure de destino. O mapeamento faz o seguinte:
-  **Conexão de rede**— garante que as VMs do Azure replicadas estejam conectadas à rede mapeada. Todos os computadores que fazem failover na mesma rede podem se conectar entre si, mesmo que tenham failover em diferentes planos de recuperação.
- **Gateway de rede**— se um gateway de rede estiver configurado na rede do Azure de destino, as VMs poderão se conectar a outras máquinas virtuais locais.

O mapeamento de rede funciona da seguinte maneira:

- Você mapeia uma rede VM do VMM de origem para uma rede virtual do Azure.
- Após o failover, as VMs do Azure na rede de origem serão conectadas à rede virtual de destino mapeada.
- As novas VMs adicionadas à rede VM de origem são conectadas à rede do Azure mapeada quando a replicação ocorre.
- Se a rede de destino tiver várias sub-redes e um dessas sub-redes tiver o mesmo nome que a sub-rede onde está localizada a máquina virtual de origem, a máquina virtual de réplica liga-se a essa sub-rede de destino após a ativação pós-falha.
- Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual liga-se à primeira sub-rede da rede.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Preparar o mapeamento de rede para replicação em um site secundário

Quando você está replicando para um site secundário, o mapeamento de rede mapeia entre redes de VM em um servidor VMM de origem e redes VM em um servidor VMM de destino. O mapeamento faz o seguinte:

- **Conexão de rede**— conecta as VMs às redes apropriadas após o failover. A VM de réplica será conectada à rede de destino que está mapeada para a rede de origem.
- **Posicionamento de VM ideal**— coloca de maneira ideal as VMs de réplica em servidores de host Hyper-V. As VMs de réplica são colocadas em hosts que podem acessar as redes de VM mapeadas.
- **Nenhum mapeamento de rede**— se você não configurar o mapeamento de rede, as VMs de réplica não serão conectadas a nenhuma rede VM após o failover.

O mapeamento de rede funciona da seguinte maneira:

- O mapeamento de rede pode ser configurado entre redes VM em dois servidores VMM, ou em um único servidor VMM se dois sites são gerenciados pelo mesmo servidor.
- Quando o mapeamento estiver configurado corretamente e a replicação estiver habilitada, uma VM no local primário será conectada a uma rede e sua réplica no local de destino será conectada à rede mapeada.
- Quando você seleciona uma rede VM de destino durante o mapeamento de rede no Site Recovery, as nuvens de origem do VMM que usam a rede VM de origem serão exibidas, juntamente com as redes VM de destino disponíveis nas nuvens de destino que são usadas para proteção.
- Se a rede de destino tiver várias sub-redes e uma dessas sub-redes tiver o mesmo nome que a sub-rede na qual a máquina virtual de origem está localizada, a VM de réplica será conectada a essa sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a VM será conectada à primeira sub-rede na rede.

## <a name="example"></a>Exemplo

Aqui está um exemplo para ilustrar esse mecanismo. Vamos pegar uma organização com dois locais em Nova York e Chicago.

**Localização** | **Servidor VMM** | **Redes VM** | **Mapeado para**
---|---|---|---
Nova Iorque | VMM-NewYork| VMNetwork1-NewYork | Mapeado para VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Não mapeado
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapeado para VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Não mapeado

Neste exemplo:

- Quando uma VM de réplica é criada para qualquer VM que está conectada a VMNetwork1-NewYork, ela será conectada a VMNetwork1-Chicago.
- Quando uma VM de réplica é criada para VMNetwork2-NewYork ou VMNetwork2-Chicago, ela não será conectada a nenhuma rede.

Veja como as nuvens do VMM são configuradas em nossa organização de exemplo e as redes lógicas associadas às nuvens.

### <a name="cloud-protection-settings"></a>Configurações de proteção de nuvem

**Nuvem protegida** | **Protegendo a nuvem** | **Rede lógica (Nova York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Configurações de rede lógica e VM

**Localização** | **Rede lógica** | **Rede VM associada**
---|---|---
Nova Iorque | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Configurações de rede de destino

Com base nessas configurações, quando você seleciona a rede VM de destino, a tabela a seguir mostra as opções que estarão disponíveis.

**Selecionar** | **Nuvem protegida** | **Protegendo a nuvem** | **Rede de destino disponível**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponível
 | GoldCloud1 | GoldCloud2 | Disponível
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Não disponível
 | GoldCloud1 | GoldCloud2 | Disponível


Se a rede de destino tiver várias sub-redes e uma dessas sub-redes tiver o mesmo nome que a sub-rede na qual a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada a essa sub-rede de destino após o failover. Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será ligada à primeira sub-rede da rede.


### <a name="failback-behavior"></a>Comportamento de failback

Para ver o que acontece no caso de failback (replicação inversa), vamos supor que VMNetwork1-NewYork seja mapeado para VMNetwork1-Chicago, com as configurações a seguir.


**VM** | **Conectado à rede VM**
---|---
VM1 | VMNetwork1-Network
VM2 (réplica de VM1) | VMNetwork1-Chicago

Com essas configurações, vamos examinar o que acontece em alguns cenários possíveis.

**Cenário** | **Saída**
---|---
Nenhuma alteração nas propriedades de rede da VM-2 após o failover. | A VM-1 permanece conectada à rede de origem.
As propriedades de rede da VM-2 são alteradas após o failover e são desconectadas. | A VM-1 está desconectada.
As propriedades de rede da VM-2 são alteradas após o failover e estão conectadas a VMNetwork2-Chicago. | Se VMNetwork2-Chicago não estiver mapeado, a VM-1 será desconectada.
O mapeamento de rede de VMNetwork1-Chicago é alterado. | A VM-1 será conectada à rede agora mapeada para VMNetwork1-Chicago.



## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](hyper-v-vmm-networking.md) Endereçamento IP após o failover para um site secundário do VMM.
- [Saiba mais](concepts-on-premises-to-azure-networking.md) Endereçamento IP após o failover para o Azure.
