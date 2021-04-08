---
title: Mapear redes virtuais entre duas regiões na Recuperação do Sítio Azure
description: Saiba mapear redes virtuais entre duas regiões do Azure para recuperação de desastres em Azure VM com recuperação do local de Azure.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: harshacs
ms.openlocfilehash: ff1f80641dc3db1f6b69fc0223c60022f8cf8435
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95811635"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Configurar o mapeamento de rede e o endereçamento IP para VNets

Este artigo descreve como mapear duas instâncias de redes virtuais Azure (VNets) localizadas em diferentes regiões do Azure, e como configurar endereços IP entre redes. O mapeamento da rede fornece um comportamento padrão para a seleção da rede alvo com base na rede de origem no momento de permitir a replicação.

## <a name="prerequisites"></a>Pré-requisitos

Antes de mapear as redes, deverá ter [VNets Azure](../virtual-network/virtual-networks-overview.md) na fonte e visar as regiões Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Configurar o mapeamento de rede manualmente (Opcional)

Redes de mapas da seguinte forma:

1. Na **Infraestrutura de Recuperação de Sítios,** clique em **+Network Mapping**.

    ![ Criar um mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. No **mapeamento de rede Adicionar,** selecione as localizações de origem e alvo. No nosso exemplo, a fonte VM está a funcionar na região da Ásia Oriental e replica-se na região do Sudeste Asiático.

    ![Selecione fonte e alvo](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Agora crie um mapeamento de rede na direção oposta. No nosso exemplo, a fonte será agora o Sudeste Asiático, e o alvo será a Ásia Oriental.

    ![Adicionar painel de mapeamento de rede - Selecione as localizações de origem e alvo para a rede alvo](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Redes de mapas quando permite a replicação

Se ainda não preparou o mapeamento da rede antes de configurar a recuperação de desastres para os VMs Azure, pode especificar uma rede-alvo quando [configurar e ativar a replicação](azure-to-azure-how-to-enable-replication.md). Quando o fazes, acontece o seguinte:

- Com base no alvo que seleciona, a Recuperação do Site cria automaticamente mapeamentos de rede da fonte para a região alvo e da região alvo para a região de origem.
- Por predefinição, a Recuperação do Site cria uma rede na região alvo idêntica à rede de origem. A Recuperação do Site adiciona **-asr** como um sufixo ao nome da rede de origem. Pode personalizar a rede alvo.
- Se o mapeamento da rede já tiver ocorrido para uma rede de origem, a rede alvo mapeada será sempre o padrão no momento de permitir replicações para mais VMs. Pode optar por alterar a rede virtual alvo, escolhendo outras opções disponíveis a partir do dropdown. 
- Para alterar a rede virtual de alvo padrão para novas replicações, é necessário modificar o mapeamento de rede existente.
- Se desejar modificar um mapeamento de rede da região A para a região B, certifique-se de que elimina primeiro o mapeamento da rede da região B para a região A. Após a eliminação inversa do mapeamento, modifique o mapeamento da rede da região A para a região B e, em seguida, crie o mapeamento inverso relevante.

>[!NOTE]
>* Modificar o mapeamento da rede apenas altera as predefinições para novas replicações VM. Não afeta as seleções de rede virtuais-alvo para as replicações existentes. 
>* Se desejar modificar a rede alvo para uma replicação existente, vá para Configurações de Computação e Rede do item replicado.

## <a name="specify-a-subnet"></a>Especificar uma sub-rede

A sub-rede do VM alvo é selecionada com base no nome da sub-rede da fonte VM.

- Se uma sub-rede com o mesmo nome que a sub-rede VM de origem estiver disponível na rede alvo, essa sub-rede está definida para o VM alvo.
- Se uma sub-rede com o mesmo nome não existir na rede alvo, a primeira sub-rede da ordem alfabética é definida como sub-rede alvo.
- Pode modificar a sub-rede alvo nas definições **de Computação e Rede** para o VM.

    ![Janela de propriedades computacional e de computação de rede](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Configurar endereço IP para VMs-alvo

O endereço IP de cada NIC numa máquina virtual alvo é configurado da seguinte forma:

- **DHCP**: Se o NIC da fonte VM utilizar o DHCP, o NIC do VM-alvo também está definido para utilizar o DHCP.
- **Endereço IP estático**: Se o NIC da fonte VM utilizar o endereço IP estático, o VM NIC alvo também utilizará um endereço IP estático.


## <a name="ip-address-assignment-during-failover"></a>Atribuição de endereço IP durante a ativação pós-falha

**Subesí redes de origem e alvo** | **Detalhes**
--- | ---
Mesmo espaço de endereço | O endereço IP da fonte VM NIC é definido como o endereço IP VM-TARGET.<br/><br/> Se o endereço não estiver disponível, o próximo endereço IP disponível é definido como o alvo.
Espaço de endereço diferente | O próximo endereço IP disponível na sub-rede-alvo é definido como o endereço VM NIC alvo.



## <a name="ip-address-assignment-during-test-failover"></a>Atribuição de endereço IP durante falha de teste

**Rede alvo** | **Detalhes**
--- | ---
A rede alvo é o VNet de falha | - O endereço IP alvo será estático com o mesmo endereço IP. <br/><br/>  - Se o mesmo endereço IP já estiver atribuído, o endereço IP é o próximo disponível no final da gama de sub-redes. Por exemplo: Se o endereço IP de origem for 10.0.0.19 e a rede de failover utilizar o intervalo 10.0.0.0/24, então o próximo endereço IP atribuído ao VM-alvo é de 10.0.0.254.
A rede alvo não é o VNet de s falha | - O endereço IP alvo será estático com o mesmo endereço IP.<br/><br/>  - Se o mesmo endereço IP já estiver atribuído, o endereço IP é o próximo disponível no final da gama de sub-redes.<br/><br/> Por exemplo: Se o endereço IP estático de origem for 10.0.0.19 e o failover estiver numa rede que não é a rede de failover, com o intervalo 10.0.0.0/24, então o endereço IP estático alvo será 10.0.0.19 se disponível, e de outra forma será 10.0.0.254.

- O VNet de falha é a rede-alvo que seleciona quando configura a recuperação de desastres.
- Recomendamos que utilize sempre uma rede de não produção para o teste de falha.
- Pode modificar o endereço IP alvo nas definições **de Computação e Rede** do VM.


## <a name="next-steps"></a>Passos seguintes

- Rever [orientação de networking](./azure-to-azure-about-networking.md) para a recuperação de desastres da Azure VM.
- [Saiba mais](site-recovery-retain-ip-azure-vm-failover.md) sobre a retenção de endereços IP após o failover.
