---
title: Gerir adaptadores de rede para recuperação de desastres no local com recuperação do local de Azure
description: Descreve como gerir interfaces de rede para recuperação de desastres no local para Azure com Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: harshacs
ms.openlocfilehash: 4dad7f76edf34782131c7c844978763cda53acc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90068120"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Gerir as interfaces de rede da VM para recuperação após desastre no local no Azure

Uma máquina virtual (VM) em Azure deve ter pelo menos uma interface de rede ligada a ela. Pode ter tantas interfaces de rede ligadas a ele como o tamanho VM suporta.

Por predefinição, a primeira interface de rede ligada a uma máquina virtual Azure é definida como a interface de rede primária. Todas as outras interfaces de rede na máquina virtual são interfaces de rede secundárias. Também por padrão, todo o tráfego de saída da máquina virtual é enviado para fora o endereço IP que é atribuído à configuração IP primária da interface de rede primária.

Num ambiente no local, máquinas virtuais ou servidores podem ter múltiplas interfaces de rede para diferentes redes dentro do ambiente. Diferentes redes são normalmente usadas para realizar operações específicas, tais como upgrades, manutenção e acesso à Internet. Quando estiver a migrar ou a falhar para o Azure a partir de um ambiente no local, tenha em mente que as interfaces de rede na mesma máquina virtual devem estar todas ligadas à mesma rede virtual.

Por predefinição, o Azure Site Recovery cria tantas interfaces de rede numa máquina virtual Azure como estão ligadas ao servidor no local. Pode evitar a criação de interfaces de rede redundantes durante a migração ou falha, editando as definições de interface de rede nas definições da máquina virtual replicada.

## <a name="select-the-target-network"></a>Selecione a rede alvo

Para máquinas virtuais VMware e físicas, e para máquinas virtuais Hyper-V (sem System Center Virtual Machine Manager), pode especificar a rede virtual alvo para máquinas virtuais individuais. Para máquinas virtuais Hiper-V geridas com O Gestor de Máquinas Virtuais, utilize [o mapeamento de rede](./hyper-v-vmm-network-mapping.md) para mapear redes VM num servidor virtual de gestor de máquinas de origem e redes Azure alvo.

1. Em **itens replicados** num cofre dos Serviços de Recuperação, selecione qualquer item replicado para aceder às definições desse item replicado.

2. Selecione o **separador Compute e Network** para aceder às definições de rede para o item replicado.

3. Nas **propriedades da Rede,** escolha uma rede virtual a partir da lista de interfaces de rede disponíveis.

    ![Definições de rede](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

A modificação da rede alvo afeta todas as interfaces de rede para essa máquina virtual específica.

Para as nuvens do Gestor de Máquinas Virtuais, modificar o mapeamento da rede afeta todas as máquinas virtuais e as suas interfaces de rede.

## <a name="select-the-target-interface-type"></a>Selecione o tipo de interface alvo

Na secção **de interfaces de Rede** do painel de **computação e rede,** pode visualizar e editar as definições de interface de rede. Também pode especificar o tipo de interface de rede alvo.

- É necessária uma interface de rede **primária** para a falha.
- Todas as outras interfaces de rede selecionadas, se houver, são interfaces de rede **secundárias.**
- **Selecione Não utilize** para excluir uma interface de rede da criação em failover.

Por predefinição, quando está a ativar a replicação, a Recuperação do Site seleciona todas as interfaces de rede detetadas no servidor no local. Marca um como **Primário** e todos os outros como **Secundário.** Quaisquer interfaces subsequentes adicionadas no servidor no local estão marcadas **Não utilize** por defeito. Quando estiver a adicionar mais interfaces de rede, certifique-se de que o tamanho do alvo da máquina virtual Azure é selecionado para acomodar todas as interfaces de rede necessárias.

## <a name="modify-network-interface-settings"></a>Modificar as definições da interface de rede

Pode modificar o endereço sub-rede e IP para as interfaces de rede de um item replicado. Se um endereço IP não for especificado, a Recuperação do Site atribuirá o próximo endereço IP disponível da sub-rede para a interface de rede em falha.

1. Selecione qualquer interface de rede disponível para abrir as definições de interface de rede.

2. Escolha a sub-rede desejada na lista de sub-redes disponíveis.

3. Introduza o endereço IP pretendido (conforme necessário).

    ![Definições de interface de rede](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Selecione **OK** para terminar a edição e volte ao **painel de computação e rede.**

5. Repita os passos 1-4 para outras interfaces de rede.

6. **Selecione Guardar** para guardar todas as alterações.

## <a name="next-steps"></a>Passos seguintes
  [Saiba mais](../virtual-network/virtual-network-network-interface-vm.md) sobre interfaces de rede para máquinas virtuais Azure.
