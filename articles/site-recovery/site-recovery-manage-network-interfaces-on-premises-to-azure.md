---
title: Gerir adaptadores de rede para recuperação de desastres no local com recuperação do site Azure
description: Descreve como gerir interfaces de rede para recuperação de desastres no local para Azure com recuperação do site Azure
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 2a4752b501e40f9e8a4f3bc82cb2533c11f9e526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954595"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Gerir interfaces de rede VM para recuperação de desastres no local para O Azure

Uma máquina virtual (VM) em Azure deve ter pelo menos uma interface de rede ligada a ela. Pode ter tantas interfaces de rede anexadas a ele como o tamanho VM suporta.

Por predefinição, a primeira interface de rede anexa a uma máquina virtual Azure é definida como a interface de rede primária. Todas as outras interfaces de rede na máquina virtual são interfaces de rede secundárias. Também por padrão, todo o tráfego de saída da máquina virtual é enviado o endereço IP que é atribuído à configuração ip primária da interface de rede primária.

Num ambiente no local, máquinas virtuais ou servidores podem ter múltiplas interfaces de rede para diferentes redes dentro do ambiente. Redes diferentes são normalmente usadas para realizar operações específicas, tais como upgrades, manutenção e acesso à Internet. Quando estiver a migrar ou a falhar para o Azure a partir de um ambiente no local, lembre-se que as interfaces de rede na mesma máquina virtual devem estar todas ligadas à mesma rede virtual.

Por padrão, a Recuperação do Site Azure cria tantas interfaces de rede numa máquina virtual Azure que estão ligadas ao servidor no local. Pode evitar criar interfaces de rede redundantes durante a migração ou failover, editando as definições da interface de rede sob as definições para a máquina virtual replicada.

## <a name="select-the-target-network"></a>Selecione a rede alvo

Para máquinas virtuais VMware e física, e para máquinas virtuais Hyper-V (sem System Center Virtual Machine Manager), pode especificar a rede virtual alvo para máquinas virtuais individuais. Para máquinas virtuais Hyper-V geridas com O Gestor de Máquinas Virtuais, utilize [o mapeamento de rede](site-recovery-network-mapping.md) para mapear redes VM num servidor de gestor de máquinas virtual de origem e redes-alvo Azure.

1. Em **itens replicados** num cofre de Serviços de Recuperação, selecione qualquer item replicado para aceder às definições para esse item replicado.

2. Selecione o separador **Compute e Rede** para aceder às definições de rede para o item replicado.

3. No âmbito **das propriedades da Rede,** escolha uma rede virtual a partir da lista de interfaces de rede disponíveis.

    ![Definições de rede](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Modificar a rede alvo afeta todas as interfaces de rede para essa máquina virtual específica.

Para as nuvens de Gestor de Máquinas Virtuais, modificar o mapeamento da rede afeta todas as máquinas virtuais e as suas interfaces de rede.

## <a name="select-the-target-interface-type"></a>Selecione o tipo de interface do alvo

Na secção de **interfaces** de rede do painel **Compute e Network,** pode visualizar e editar as definições de interface de rede. Também pode especificar o tipo de interface de rede alvo.

- É necessária uma interface de rede **primária** para a falha.
- Todas as outras interfaces de rede selecionadas, se houver, são interfaces de rede **secundárias.**
- Selecione **Não utilize** para excluir uma interface de rede da criação no failover.

Por predefinição, quando está a permitir a replicação, a Recuperação do Site seleciona todas as interfaces de rede detetadas no servidor no local. Marca um como **Primário** e todos os outros como **Secundário.** Quaisquer interfaces subsequentes adicionadas no servidor no local estão marcadas **Não utilize** por predefinição. Quando estiver a adicionar mais interfaces de rede, certifique-se de que o tamanho de alvo da máquina virtual Azure correto é selecionado para acomodar todas as interfaces de rede necessárias.

## <a name="modify-network-interface-settings"></a>Modificar as definições de interface de rede

Pode modificar a sub-rede e o endereço IP para as interfaces de rede de um item replicado. Se um endereço IP não for especificado, a Recuperação do Site atribuirá o próximo endereço IP disponível da subnet à interface de rede no failover.

1. Selecione qualquer interface de rede disponível para abrir as definições da interface da rede.

2. Escolha a sub-rede desejada na lista de subredes disponíveis.

3. Introduza o endereço IP desejado (conforme necessário).

    ![Definições de interface de rede](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Selecione **OK** para terminar a edição e volte ao painel **Compute e Network.**

5. Repita os passos 1-4 para outras interfaces de rede.

6. Selecione **Guardar** para guardar todas as alterações.

## <a name="next-steps"></a>Passos seguintes
  [Saiba mais](../virtual-network/virtual-network-network-interface-vm.md) sobre interfaces de rede para máquinas virtuais Azure.
