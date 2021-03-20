---
title: Sobre o mapeamento da rede Hyper-V (com VMM) com recuperação do site
description: Descreve como configurar o mapeamento de rede para a recuperação de desastres de Hiper-VMs (geridos em nuvens VMM) para Azure, com Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "74082564"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Preparar mapeamento de rede para recuperação de desastres Hiper-V VM para Azure


Este artigo ajuda-o a compreender e a preparar-se para o mapeamento de rede quando replica as nuvens de Hiper-V VMs no System Center Virtual Machine Manager (VMM) para Azure, ou para um site secundário, utilizando o serviço de Recuperação do [Local Azure.](site-recovery-overview.md)


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Preparar mapeamento de rede para replicação ao Azure

Quando está a replicar para o Azure, mapes de mapeamento de rede entre redes VM num servidor VMM de origem e redes virtuais Azure. O mapeamento faz o seguinte:
-  **Ligação à** rede — Garante que os VMs Azure replicados estão ligados à rede mapeada. Todas as máquinas que falham na mesma rede podem ligar-se entre si, mesmo que tenham falhado em diferentes planos de recuperação.
- **Gateway de rede**— Se um gateway de rede for configurado na rede Azure alvo, os VMs podem ligar-se a outras máquinas virtuais no local.

O mapeamento da rede funciona da seguinte forma:

- Mapeia uma rede VM de origem para uma rede virtual Azure.
- Após o failover Azure VMs na rede de origem será ligado à rede virtual alvo mapeada.
- Os novos VM adicionados à rede VM de origem estão ligados à rede Azure mapeada quando ocorre a replicação.
- Se a rede de destino tiver várias sub-redes e um dessas sub-redes tiver o mesmo nome que a sub-rede onde está localizada a máquina virtual de origem, a máquina virtual de réplica liga-se a essa sub-rede de destino após a ativação pós-falha.
- Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual liga-se à primeira sub-rede da rede.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Preparar mapeamento de rede para replicação num local secundário

Quando está a replicar-se num site secundário, mapeias de mapeamento de rede entre redes VM num servidor VMM de origem e redes VM num servidor VMM alvo. O mapeamento faz o seguinte:

- **Ligação à** rede — Liga os VMs a redes apropriadas após a falha. A réplica VM será ligada à rede alvo que está mapeada para a rede de origem.
- **Colocação VM ideal**— Coloca idealmente os VMs réplicas nos servidores de anfitriões Hiper-V. Os VMs de réplica são colocados em anfitriões que podem aceder às redes VM mapeadas.
- **Sem mapeamento de rede**— Se não configurar o mapeamento da rede, as réplicas de VMs não serão ligadas a quaisquer redes VM após a falha.

O mapeamento da rede funciona da seguinte forma:

- O mapeamento de rede pode ser configurado entre redes VM em dois servidores VMM ou num único servidor VMM se dois sites forem geridos pelo mesmo servidor.
- Quando o mapeamento é configurado corretamente e a replicação está ativada, um VM na localização primária será ligado a uma rede, e a sua réplica no local alvo será ligada à sua rede mapeada.
- Quando selecionar uma rede VM alvo durante o mapeamento da rede na Recuperação do Local, serão apresentadas as nuvens de origem VMM que utilizam a rede VM de origem, juntamente com as redes VM-alvo disponíveis nas nuvens-alvo que são utilizadas para proteção.
- Se a rede alvo tiver várias sub-redes e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, então a réplica VM será ligada à sub-rede-alvo após a falha. Se não houver uma sub-rede de alvo com um nome correspondente, o VM será ligado à primeira sub-rede da rede.

## <a name="example"></a>Exemplo

Aqui está um exemplo para ilustrar este mecanismo. Vamos a uma organização com duas localizações em Nova Iorque e Chicago.

**Localização** | **Servidor VMM** | **Redes VM** | **Mapeado para**
---|---|---|---
Nova Iorque | VMM-NewYork| VMNetwork1-NewYork | Mapeado para VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Não mapeado
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapeado para VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Não mapeado

Neste exemplo:

- Quando uma réplica VM é criada para qualquer VM que esteja ligado à VMNetwork1-NewYork, será ligado à VMNetwork1-Chicago.
- Quando uma réplica VM é criada para VMNetwork2-NewYork ou VMNetwork2-Chicago, não estará ligada a nenhuma rede.

Eis como as nuvens de VMM são criadas na nossa organização de exemplo, e as redes lógicas associadas às nuvens.

### <a name="cloud-protection-settings"></a>Definições de proteção de nuvem

**Nuvem protegida** | **Proteger a nuvem** | **Rede lógica (Nova Iorque)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Definições de rede lógica e VM

**Localização** | **Rede lógica** | **Rede VM associada**
---|---|---
Nova Iorque | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | Rede Lógica2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Definições de rede de alvo

Com base nestas definições, quando seleciona a rede VM alvo, a tabela a seguir mostra as escolhas que estarão disponíveis.

**Selecionar** | **Nuvem protegida** | **Proteger a nuvem** | **Rede-alvo disponível**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponível
 | GoldCloud1 | GoldCloud2 | Disponível
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Não disponível
 | GoldCloud1 | GoldCloud2 | Disponível


Se a rede alvo tiver várias sub-redes e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, então a máquina virtual réplica será ligada à sub-rede do alvo após a falha. Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será ligada à primeira sub-rede da rede.


### <a name="failback-behavior"></a>Comportamento de falha

Para ver o que acontece no caso de failback (replicação inversa), vamos supor que VMNetwork1-NewYork está mapeada para VMNetwork1-Chicago, com as seguintes definições.


**VM** | **Ligado à rede VM**
---|---
VM1 | VMNetwork1-Network
VM2 (réplica de VM1) | VMNetwork1-Chicago

Com estas definições, vamos rever o que acontece em alguns cenários possíveis.

**Cenário** | **Resultado**
---|---
Não há alteração nas propriedades da rede de VM-2 após a falha. | O VM-1 continua ligado à rede de origem.
As propriedades da rede de VM-2 são alteradas após a falha e são desligadas. | O VM-1 está desligado.
As propriedades da rede de VM-2 são alteradas após a falha e estão ligadas à VMNetwork2-Chicago. | Se VMNetwork2-Chicago não estiver mapeada, o VM-1 será desligado.
O mapeamento da rede de VMNetwork1-Chicago é alterado. | O VM-1 será ligado à rede agora mapeada para vMNetwork1-Chicago.



## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](hyper-v-vmm-networking.md) Endereço IP após falha para um site de VMM secundário.
- [Saiba mais](concepts-on-premises-to-azure-networking.md) Endereço IP após falha na Azure.
