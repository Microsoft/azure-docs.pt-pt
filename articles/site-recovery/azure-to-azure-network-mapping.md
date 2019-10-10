---
title: Mapear redes virtuais entre duas regiões do Azure no Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre o failover para o Azure ou para um datacenter secundário.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 6249a3c1c8ea3be02ca802d6be7e720bd900f675
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178102"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Configurar o mapeamento de rede e o endereçamento IP para VNets

Este artigo descreve como mapear duas instâncias de redes virtuais do Azure (VNets) localizadas em diferentes regiões do Azure e como configurar o endereçamento IP entre redes. O mapeamento de rede fornece um comportamento padrão para a seleção de rede de destino com base na rede de origem no momento da habilitação da replicação.

## <a name="prerequisites"></a>Pré-requisitos

Antes de mapear redes, você deve ter o [Azure VNets](../virtual-network/virtual-networks-overview.md) nas regiões de origem e de destino do Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Configurar o mapeamento de rede manualmente (opcional)

Mapeie as redes da seguinte maneira:

1. Em **site Recovery infraestrutura**, clique em **+ mapeamento de rede**.

    ![ Criar um mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. Em **Adicionar mapeamento de rede**, selecione os locais de origem e de destino. Em nosso exemplo, a VM de origem está em execução na região de Ásia Oriental e é replicada para a região sudeste asiático.

    ![Selecionar origem e destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Agora, crie um mapeamento de rede na direção oposta. Em nosso exemplo, a fonte agora será Sudeste Asiático e o destino será Ásia Oriental.

    ![Painel Adicionar mapeamento de rede – selecione os locais de origem e de destino para a rede de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapear redes ao habilitar a replicação

Se você não preparou o mapeamento de rede antes de configurar a recuperação de desastre para VMs do Azure, você pode especificar uma rede de destino ao [configurar e habilitar a replicação](azure-to-azure-how-to-enable-replication.md). Quando você fizer isso, acontecerá o seguinte:

- Com base no destino selecionado, Site Recovery cria automaticamente mapeamentos de rede da região de origem para destino e da região de destino para origem.
- Por padrão, Site Recovery cria uma rede na região de destino que é idêntica à rede de origem. Site Recovery adiciona **-ASR** como um sufixo ao nome da rede de origem. Você pode personalizar a rede de destino.
- Se o mapeamento de rede já tiver ocorrido para uma rede de origem, a rede de destino mapeada sempre será a padrão no momento da habilitação de replicações para mais VMs. Você pode optar por alterar a rede virtual de destino escolhendo outras opções disponíveis na lista suspensa. 
- Para alterar a rede virtual de destino padrão para novas replicações, você precisa modificar o mapeamento de rede existente.
- Se você quiser modificar um mapeamento de rede da região A para a região B, certifique-se de primeiro excluir o mapeamento de rede da região B para a região A. Após a exclusão de mapeamento reverso, modifique o mapeamento de rede da região A para a região B e crie o mapeamento inverso relevante.

>[!NOTE]
>* Modificar o mapeamento de rede altera apenas os padrões para novas replicações de VM. Ele não afeta as seleções de rede virtual de destino para as replicações existentes. 
>* Se você quiser modificar a rede de destino para uma replicação existente, vá para configurações de computação e rede do item replicado.

## <a name="specify-a-subnet"></a>Especificar uma sub-rede

A sub-rede da VM de destino é selecionada com base no nome da sub-rede da VM de origem.

- Se uma sub-rede com o mesmo nome da sub-rede VM de origem estiver disponível na rede de destino, essa sub-rede será definida para a VM de destino.
- Se uma sub-rede com o mesmo nome não existir na rede de destino, a primeira sub-rede na ordem alfabética será definida como a sub-rede de destino.
- Você pode modificar a sub-rede de destino nas configurações de **computação e rede** da VM.

    ![Janela Propriedades de computação de computação e rede](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Configurar o endereçamento IP para VMs de destino

O endereço IP para cada NIC em uma máquina virtual de destino é configurado da seguinte maneira:

- **DHCP**: se a NIC da VM de origem usar DHCP, a NIC da VM de destino também será definida para usar DHCP.
- **Endereço IP estático**: se a NIC da VM de origem usar endereçamento IP estático, a NIC da VM de destino também usará um endereço IP estático.


## <a name="ip-address-assignment-during-failover"></a>Atribuição de endereço IP durante a ativação pós-falha

**Sub-redes de origem e de destino** | **Detalhes**
--- | ---
Mesmo espaço de endereço | O endereço IP da NIC da VM de origem é definido como o endereço IP da NIC da VM de destino.<br/><br/> Se o endereço não estiver disponível, o próximo endereço IP disponível será definido como o destino.

Espaço de endereço diferente<br/><br/> O próximo endereço IP disponível na sub-rede de destino é definido como o endereço NIC da VM de destino.



## <a name="ip-address-assignment-during-test-failover"></a>Atribuição de endereço IP durante o failover de teste

**Rede de destino** | **Detalhes**
--- | ---
A rede de destino é a VNet de failover | -O endereço IP de destino é estático, mas não o mesmo endereço IP que o reservado para failover.<br/><br/>  -O endereço atribuído é o próximo endereço disponível do final do intervalo de sub-rede.<br/><br/> Por exemplo: se o endereço IP de origem for 10.0.0.19 e a rede de failover usar o intervalo 10.0.0.0/24, o próximo endereço IP atribuído à VM de destino será 10.0.0.254.
A rede de destino não é a VNet de failover | -O endereço IP de destino será estático com o mesmo endereço IP reservado para failover.<br/><br/>  -Se o mesmo endereço IP já estiver atribuído, o endereço IP será o próximo disponível no final do intervalo de sub-rede.<br/><br/> Por exemplo: se o endereço IP estático de origem for 10.0.0.19 e o failover estiver em uma rede que não é a rede de failover, com o intervalo 10.0.0.0/24, o endereço IP estático de destino será 10.0.0.0.19 se disponível e, caso contrário, será 10.0.0.254.

- A VNet de failover é a rede de destino que você seleciona ao configurar a recuperação de desastre.
- Recomendamos que você sempre use uma rede que não seja de produção para o failover de teste.
- Você pode modificar o endereço IP de destino nas configurações de **computação e rede** da VM.


## <a name="next-steps"></a>Passos seguintes

- Examine as [diretrizes de rede](site-recovery-azure-to-azure-networking-guidance.md) para a recuperação de desastres de VM do Azure.
- [Saiba mais](site-recovery-retain-ip-azure-vm-failover.md) sobre a retenção de endereços IP após o failover.
