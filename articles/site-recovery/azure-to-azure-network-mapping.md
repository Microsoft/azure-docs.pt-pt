---
title: Mapear redes virtuais entre duas regiões na Recuperação do Sítio Do Azure
description: Saiba mapear redes virtuais entre duas regiões azure para recuperação de desastres Azure VM com recuperação do site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mayg
ms.openlocfilehash: b8f0512f978f25ca196ad6e9a7a03243c47f0662
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258086"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Configurar o mapeamento da rede e o endereço IP para VNets

Este artigo descreve como mapear duas instâncias de redes virtuais Azure (VNets) localizadas em diferentes regiões do Azure, e como configurar endereços IP entre redes. O mapeamento da rede fornece um comportamento padrão para a seleção da rede alvo com base na rede de origem no momento da replicação.

## <a name="prerequisites"></a>Pré-requisitos

Antes de mapear redes, deve ter [VNets Azure](../virtual-network/virtual-networks-overview.md) nas regiões de origem e alvo de Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Configurar o mapeamento da rede manualmente (Opcional)

Redes de mapas da seguinte forma:

1. Na Infraestrutura de Recuperação do **Local,** clique em **+Mapeamento de rede**.

    ![ Criar um mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. Em **Adicionar mapeamento**de rede, selecione a origem e os locais-alvo. No nosso exemplo, a fonte vm está a funcionar na região da Ásia Oriental e replica-se para a região do Sudeste Asiático.

    ![Selecione fonte e alvo](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Agora crie um mapeamento de rede na direção oposta. No nosso exemplo, a fonte será agora o Sudeste Asiático, e o alvo será a Ásia Oriental.

    ![Adicione painel de mapeamento de rede - Selecione locais de origem e alvo para a rede alvo](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Redes de mapas quando permite a replicação

Se ainda não tiver preparado o mapeamento da rede antes de configurar a recuperação de desastres para Os VMs Do Azure, pode especificar uma rede de destino quando [configurar e ativar](azure-to-azure-how-to-enable-replication.md)a replicação . Quando o fizeres, acontece o seguinte:

- Com base no alvo selecionado, a Recuperação do Site cria automaticamente mapeamentos de rede da fonte para a região alvo, e do alvo para a região de origem.
- Por padrão, a Recuperação do Site cria uma rede na região alvo idêntica à rede de origem. A Recuperação do Site adiciona **-asr** como um sufixo ao nome da rede de origem. Pode personalizar a rede de destino.
- Se o mapeamento da rede já tiver ocorrido para uma rede de origem, a rede de destino mapeada será sempre o padrão no momento de permitir replicações para mais VMs. Pode optar por alterar a rede virtual alvo, escolhendo outras opções disponíveis a partir do dropdown. 
- Para alterar a rede virtual de destino padrão para novas replicações, é necessário modificar o mapeamento de rede existente.
- Se pretender modificar um mapeamento de rede da região A para a região B, certifique-se de que elimina primeiro o mapeamento da rede da região B para a região A. Após a eliminação do mapeamento inverso, modifique o mapeamento da rede da região A para a região B e, em seguida, crie o mapeamento inverso relevante.

>[!NOTE]
>* Modificar o mapeamento da rede apenas altera as predefinições para novas replicações vm. Não afeta as seleções de rede virtuais-alvo para as replicações existentes. 
>* Se pretender modificar a rede-alvo para uma replicação existente, vá às Definições computacionais e de rede do item replicado.

## <a name="specify-a-subnet"></a>Especificar uma sub-rede

A sub-rede do VM alvo é selecionada com base no nome da sub-rede do VM de origem.

- Se uma sub-rede com o mesmo nome que a sub-rede VM de origem estiver disponível na rede-alvo, essa sub-rede está definida para o VM-alvo.
- Se uma sub-rede com o mesmo nome não existir na rede alvo, a primeira sub-rede na ordem alfabética é definida como a sub-rede alvo.
- Pode modificar a sub-rede de destino nas definições **de Compute e Rede** para o VM.

    ![Janela de propriedades computacionais e computacionais](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Configurar endereçoIP para VMs-alvo

O endereço IP de cada NIC numa máquina virtual alvo está configurado da seguinte forma:

- **DHCP**: Se o NIC da fonte VM utilizar DHCP, o NIC do VM alvo também está definido para utilizar dHCP.
- **Endereço IP estático**: Se o NIC da fonte VM utilizar endereçoip estático, o VM NIC alvo também utilizará um endereço IP estático.


## <a name="ip-address-assignment-during-failover"></a>Atribuição de endereço IP durante a ativação pós-falha

**Subredes de origem e alvo** | **Detalhes**
--- | ---
Mesmo espaço de endereço | O endereço IP da fonte VM NIC é definido como o endereço IP VM NIC alvo.<br/><br/> Se o endereço não estiver disponível, o próximo endereço IP disponível é definido como o alvo.
Espaço de endereçodiferente | O próximo endereço IP disponível na sub-rede-alvo é definido como o endereço VM NIC alvo.



## <a name="ip-address-assignment-during-test-failover"></a>Atribuição de endereçoIP durante a falha do teste

**Rede de destino** | **Detalhes**
--- | ---
A rede de destino é o VNet failover | - O endereço IP do alvo será estático com o mesmo endereço IP. <br/><br/>  - Se o mesmo endereço IP já estiver atribuído, o endereço IP é o próximo disponível no final da gama de sub-redes. Por exemplo: Se o endereço IP de origem for 10.0.0.19 e a rede failover utilizar o intervalo 10.0.0.0/24, então o próximo endereço IP atribuído ao VM-alvo é de 10.0.0.254.
A rede de alvos não é a VNet falhada | - O endereço IP do alvo será estático com o mesmo endereço IP.<br/><br/>  - Se o mesmo endereço IP já estiver atribuído, o endereço IP é o próximo disponível no final da gama de sub-redes.<br/><br/> Por exemplo: Se o endereço IP estático de origem for 10.0.0.19 e o failover estiver numa rede que não seja a rede failover, com o intervalo 10.0.0.0.0/24, então o endereço IP estático alvo será 10.0.0.19 se disponível, e caso contrário será 10.0.0.254.

- O Failover VNet é a rede de destino que seleciona quando configura a recuperação de desastres.
- Recomendamos que utilize sempre uma rede de não produção para o teste falhar.
- Pode modificar o endereço IP do alvo nas definições **de Compute e Rede** do VM.


## <a name="next-steps"></a>Passos seguintes

- Reveja [as orientações de networking](site-recovery-azure-to-azure-networking-guidance.md) para a recuperação de desastres da VM Azure.
- [Saiba mais](site-recovery-retain-ip-azure-vm-failover.md) sobre a retenção de endereços IP após a falha.
