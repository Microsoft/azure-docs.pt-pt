---
title: Sobre o mapeamento da rede Hyper-V (com VMM) com a Recuperação do Site
description: Descreve como configurar o mapeamento da rede para a recuperação de desastres de VMs Hiper-V (geridos em nuvens VMM) para Azure, com A Recuperação do Site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082564"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Prepare mapeamento de rede para recuperação de desastres de VM Hiper-V m para Azure


Este artigo ajuda-o a compreender e a preparar-se para o mapeamento da rede quando replica os VMs Hiper-V em nuvens de Gestor de Máquinas Virtuais (VMM) do System Center para o Azure, ou para um site secundário, utilizando o serviço de Recuperação de [Sítios Azure.](site-recovery-overview.md)


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Preparar mapeamento de rede para replicação ao Azure

Quando estiver a replicar-se para o Azure, os mapas de mapeamento de rede entre redes VM num servidor VMM de origem e redes virtuais do Target Azure. O mapeamento faz o seguinte:
-  **Ligação de rede**— Garante que os VMs Azure replicados estão ligados à rede mapeada. Todas as máquinas que falham na mesma rede podem ligar-se umas às outras, mesmo que tenham falhado em diferentes planos de recuperação.
- **Gateway**de rede — Se for criado um portal de rede na rede Target Azure, os VMs podem ligar-se a outras máquinas virtuais no local.

O mapeamento da rede funciona da seguinte forma:

- Mapeia uma rede VMM VM de origem para uma rede virtual Azure.
- Após falhas, os VMs Azure na rede de origem serão ligados à rede virtual de destino mapeada.
- Os novos VMs adicionados à rede VM de origem estão ligados à rede Azure mapeada quando ocorre a replicação.
- Se a rede de destino tiver várias sub-redes e um dessas sub-redes tiver o mesmo nome que a sub-rede onde está localizada a máquina virtual de origem, a máquina virtual de réplica liga-se a essa sub-rede de destino após a ativação pós-falha.
- Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual liga-se à primeira sub-rede da rede.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Preparar o mapeamento da rede para a replicação a um local secundário

Quando está a replicar-se para um site secundário, mapas de mapeamento de rede entre redes VM num servidor VMM de origem e redes VM num servidor VMM alvo. O mapeamento faz o seguinte:

- **Ligação de rede**— Liga VMs a redes apropriadas após a falha. A réplica VM estará ligada à rede de alvos que está mapeada para a rede de origem.
- **Colocação VM ideal**— Idealmente coloca as VMs réplicas nos servidores de anfitriões Hyper-V. Os VMs réplicas são colocados em anfitriões que podem aceder às redes VM mapeadas.
- **Sem mapeamento**de rede — Se não configurar o mapeamento da rede, os VMs de réplica não serão ligados a nenhuma rede VM após a falha.

O mapeamento da rede funciona da seguinte forma:

- O mapeamento da rede pode ser configurado entre redes VM em dois servidores VMM, ou num único servidor VMM se dois sites forem geridos pelo mesmo servidor.
- Quando o mapeamento estiver configurado corretamente e a replicação estiver ativada, um VM no local principal será ligado a uma rede, e a sua réplica no local do alvo será ligada à sua rede mapeada.
- Quando selecionar uma rede VM alvo durante o mapeamento da rede na Recuperação do Local, as nuvens de origem VMM que utilizam a rede VM de origem serão exibidas, juntamente com as redes VM-alvo disponíveis nas nuvens-alvo que são usadas para proteção.
- Se a rede alvo tiver várias subredes e uma dessas subredes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, então a réplica VM será ligada a essa sub-rede-alvo após a falha. Se não houver uma sub-rede de alvo com um nome correspondente, o VM estará ligado à primeira sub-rede da rede.

## <a name="example"></a>Exemplo

Aqui está um exemplo para ilustrar este mecanismo. Vamos fazer uma organização com duas localizações em Nova Iorque e Chicago.

**Localização** | **Servidor VMM** | **Redes VM** | **Mapeado para**
---|---|---|---
Nova Iorque | VMM-Nova Iorque| VMNetwork1-NewYork | Mapeado para VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Não mapeado
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapeado para VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Não mapeado

Neste exemplo:

- Quando uma réplica vM é criada para qualquer VM ligado ao VMNetwork1-NewYork, será ligado ao VMNetwork1-Chicago.
- Quando uma réplica vM é criada para VMNetwork2-NewYork ou VMNetwork2-Chicago, não estará ligada a nenhuma rede.

Eis como as nuvens de VMM são criadas na nossa organização de exemplo, e as redes lógicas associadas às nuvens.

### <a name="cloud-protection-settings"></a>Definições de proteção de nuvem

**Nuvem protegida** | **Proteger a nuvem** | **Rede lógica (Nova Iorque)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Definições lógicas e vm da rede

**Localização** | **Rede lógica** | **Rede VM associada**
---|---|---
Nova Iorque | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Definições de rede de destino

Com base nestas definições, quando seleciona a rede VM alvo, a tabela a seguir mostra as escolhas que estarão disponíveis.

**Selecionar** | **Nuvem protegida** | **Proteger a nuvem** | **Rede-alvo disponível**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponível
 | GoldCloud1 | GoldCloud2 | Disponível
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Não disponível
 | GoldCloud1 | GoldCloud2 | Disponível


Se a rede alvo tiver várias subredes e uma dessas subredes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, então a máquina virtual de réplica será ligada a essa sub-rede alvo após a falha. Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será ligada à primeira sub-rede da rede.


### <a name="failback-behavior"></a>Comportamento de recuo

Para ver o que acontece no caso de failback (replicação inversa), vamos assumir que o VMNetwork1-NewYork está mapeado para VMNetwork1-Chicago, com as seguintes configurações.


**VM** | **Ligado à rede VM**
---|---
VM1 | Rede VMNetwork1
VM2 (réplica de VM1) | VMNetwork1-Chicago

Com estas configurações, vamos rever o que acontece em alguns cenários possíveis.

**Cenário** | **Resultado**
---|---
Nenhuma alteração nas propriedades da rede de VM-2 após a falha. | O VM-1 permanece ligado à rede de origem.
As propriedades da rede de VM-2 são alteradas após a falha e são desligadas. | O VM-1 está desligado.
As propriedades da rede de VM-2 são alteradas após a falha e estão ligadas ao VMNetwork2-Chicago. | Se o VMNetwork2-Chicago não estiver mapeado, o VM-1 será desligado.
O mapeamento da rede da VMNetwork1-Chicago é alterado. | O VM-1 estará ligado à rede agora mapeada para vMNetwork1-Chicago.



## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre](hyper-v-vmm-networking.md) Endereço IP após falha para um site de VMM secundário.
- [Saiba mais sobre](concepts-on-premises-to-azure-networking.md) Endereço IP após falha para Azure.
