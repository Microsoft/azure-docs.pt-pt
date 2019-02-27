---
title: Mapear as redes virtuais entre duas regiões do Azure no Azure Site Recovery | Documentos da Microsoft
description: O Azure Site Recovery coordena a replicação, ativação pós-falha e recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre a ativação pós-falha para o Azure ou para um datacenter secundário.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: b52bdd5948676c09c433cbda249248ce26ce8a48
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866033"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Configurar o mapeamento da rede e endereçamento de IP para redes virtuais

Este artigo descreve como mapear duas instâncias de redes virtuais do Azure (VNets) localizados em diferentes regiões do Azure e como configurar o endereçamento de IP entre redes. Mapeamento da rede garante que uma VM replicada é criada no destino do Azure a região é criada na VNet que está mapeada para a VNet da VM de origem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de mapear redes, deve ter [VNets do Azure](../virtual-network/virtual-networks-overview.md) na origem e as regiões do Azure de destino. 

## <a name="set-up-network-mapping"></a>Configurar o mapeamento de rede

Mapear as redes da seguinte forma:

1. Na **infraestrutura do Site Recovery**, clique em **+ mapeamento da rede**.

    ![ Criar um mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. Na **adicionar mapeamento da rede**, selecione a origem e as localizações de destino. No nosso exemplo, a origem de VM está em execução na região Ásia Oriental e replica para a região do Sudeste asiático.

    ![Selecione a origem e destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Agora, crie um mapeamento de rede no diretório oposto. No nosso exemplo, a origem será agora Sudeste asiático e o destino será Ásia Oriental.

    ![Adicionar o painel de mapeamento de rede - Selecione as localizações de origem e destino para a rede de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapear as redes quando ativa a replicação

Se ainda não preparado o mapeamento da rede antes de configurar a recuperação após desastre para VMs do Azure, pode especificar um destino de rede quando [configurar e ativar a replicação](azure-to-azure-how-to-enable-replication.md). Quando faz isso o seguinte ocorre:

- Com base no destino que selecionar, recuperação de sites cria automaticamente os mapeamentos de rede de origem para a região de destino e de destino para a região de origem.
- Por predefinição, o Site Recovery cria uma rede na região de destino que é idêntica à rede de origem. Site Recovery acrescenta **-asr** como sufixo ao nome da rede de origem. Pode personalizar a rede de destino.
- Se já tiver ocorrido mapeamento da rede, não é possível alterar a rede virtual de destino, quando ativa a replicação. Para alterar a rede virtual de destino, terá de modificar o mapeamento de rede existente.
- Se modificar um mapeamento de rede de região A região B, certifique-se de que também modificar o mapeamento de rede de região B para a região A.]

## <a name="specify-a-subnet"></a>Especifique uma sub-rede

A sub-rede de destino que VM é selecionada com base no nome da sub-rede de VM de origem.

- Se uma sub-rede com o mesmo nome que a sub-rede VM de origem está disponível na rede de destino, que a sub-rede está definida para a VM de destino.
- Se uma sub-rede com o mesmo nome não existir na rede de destino, a primeira sub-rede na ordem alfabética está definida como a sub-rede de destino.
- Pode modificar o do **computação e rede** definições para a VM.

    ![Janela de propriedades de computação de computação e rede](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Configurar o endereçamento de IP para VMs de destino

O endereço IP para cada NIC numa máquina virtual de destino está configurado da seguinte forma:

- **DHCP**: Se a NIC de VM de origem utiliza DHCP, a NIC de VM de destino também é definida para utilizar DHCP.
- **Endereço IP estático**: Se a NIC da origem de VM utiliza endereçamento IP estático, o NIC de VM de destino também irá utilizar um endereço IP estático.


## <a name="ip-address-assignment-during-failover"></a>Atribuição de endereços IP durante a ativação pós-falha

**Sub-redes de origem e destino** | **Detalhes**
--- | ---
Mesmo espaço de endereços | Endereço IP da NIC de VM de origem está definido como o endereço IP de NIC de VM de destino.<br/><br/> Se o endereço não estiver disponível, o endereço IP disponível seguinte está definido como o destino.
Espaço de endereços diferente<br/><br/> O endereço seguinte disponível de IP na sub-rede de destino está definido como o endereço da NIC de VM de destino.



## <a name="ip-address-assignment-during-test-failover"></a>Atribuição de endereços IP durante a ativação pós-falha de teste

**Rede de destino** | **Detalhes**
--- | ---
Rede de destino é a ativação pós-falha de VNet | -Endereço IP destino é estático, mas não o mesmo endereço IP que foi reservado para ativação pós-falha.<br/><br/>  -O endereço atribuído é o endereço seguinte disponível do fim do intervalo de sub-rede.<br/><br/> Por exemplo: Se o endereço IP de origem é 10.0.0.19 e rede de ativação pós-falha utiliza 10.0.0.0/24 intervalo, em seguida, o seguinte endereço IP atribuído à VM de destino é 10.0.0.254.
Rede de destino não está a ativação pós-falha de VNet | -Endereço IP de destino será estático com o mesmo endereço IP reservado para ativação pós-falha.<br/><br/>  – Se o mesmo endereço IP já está atribuído, em seguida, o endereço IP é o seguinte disponível em cada um do intervalo de sub-rede.<br/><br/> Por exemplo: Se o endereço IP estático de origem é 10.0.0.19 e ativação pós-falha estiver numa rede que não é a rede de ativação pós-falha, com 10.0.0.0/24 intervalo, em seguida, o endereço IP estático de destino será 10.0.0.0.19 se estiver disponível e, caso contrário, será 10.0.0.254.

- A ativação pós-falha VNet é a rede de destino que selecionou quando configurou a recuperação após desastre.
- Recomendamos que utilize sempre uma rede de não produção para ativação pós-falha de teste.
- Pode modificar o endereço IP de destino no **computação e rede** definições da VM.


## <a name="next-steps"></a>Passos Seguintes

- Revisão [documentação de orientação de rede](site-recovery-azure-to-azure-networking-guidance.md) para recuperação após desastre de VM do Azure.
- [Saiba mais](site-recovery-retain-ip-azure-vm-failover.md) sobre retendo os endereços IP após a ativação pós-falha.

Se a rede de destino escolhida é vnet em modo de ativação pós-falha"e o ponto de 2ª dizer"Se a rede de destino escolhida é diferente da vnet em modo de ativação pós-falha, mas tem o mesmo intervalo de sub-rede de vnet de ativação pós-falha"