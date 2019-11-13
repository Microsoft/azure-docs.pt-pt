---
title: Gerenciar adaptadores de rede para recuperação de desastre local com o Azure Site Recovery
description: Descreve como gerenciar interfaces de rede para recuperação de desastre local no Azure com Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 2a4752b501e40f9e8a4f3bc82cb2533c11f9e526
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954595"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Gerenciar interfaces de rede VM para recuperação de desastre local no Azure

Uma VM (máquina virtual) no Azure deve ter pelo menos uma interface de rede anexada a ela. Ele pode ter tantos adaptadores de rede anexados quanto o tamanho da VM dá suporte.

Por padrão, a primeira interface de rede anexada a uma máquina virtual do Azure é definida como a interface de rede principal. Todas as outras interfaces de rede na máquina virtual são interfaces de rede secundárias. Além disso, por padrão, todo o tráfego de saída da máquina virtual é enviado ao endereço IP atribuído à configuração de IP primário da interface de rede primária.

Em um ambiente local, máquinas virtuais ou servidores podem ter várias interfaces de rede para diferentes redes dentro do ambiente. Redes diferentes geralmente são usadas para executar operações específicas, como atualizações, manutenção e acesso à Internet. Quando estiver migrando ou fazendo failover para o Azure de um ambiente local, lembre-se de que os adaptadores de rede na mesma máquina virtual devem estar conectados à mesma rede virtual.

Por padrão, Azure Site Recovery cria tantas interfaces de rede em uma máquina virtual do Azure quanto estão conectadas ao servidor local. Você pode evitar a criação de interfaces de rede redundantes durante a migração ou o failover editando as configurações de interface de rede nas configurações da máquina virtual replicada.

## <a name="select-the-target-network"></a>Selecionar a rede de destino

Para máquinas físicas e VMware, e para máquinas virtuais do Hyper-V (sem System Center Virtual Machine Manager), você pode especificar a rede virtual de destino para máquinas virtuais individuais. Para máquinas virtuais Hyper-V gerenciadas com Virtual Machine Manager, use o [mapeamento de rede](site-recovery-network-mapping.md) para mapear redes VM em um servidor de Virtual Machine Manager de origem e redes do Azure de destino.

1. Em **itens replicados** em um cofre dos serviços de recuperação, selecione qualquer item replicado para acessar as configurações desse item replicado.

2. Selecione a guia **computação e rede** para acessar as configurações de rede para o item replicado.

3. Em **Propriedades da rede**, escolha uma rede virtual na lista de interfaces de rede disponíveis.

    ![Configurações de rede](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Modificar a rede de destino afeta todas as interfaces de rede para essa máquina virtual específica.

Para Virtual Machine Manager nuvens, modificar o mapeamento de rede afeta todas as máquinas virtuais e suas interfaces de rede.

## <a name="select-the-target-interface-type"></a>Selecionar o tipo de interface de destino

Na seção **interfaces de rede** do painel **computação e rede** , você pode exibir e editar as configurações de interface de rede. Você também pode especificar o tipo de interface de rede de destino.

- Uma interface de rede **principal** é necessária para o failover.
- Todas as outras interfaces de rede selecionadas, se houver, são interfaces de rede **secundárias** .
- Selecione não **usar** para excluir uma interface de rede da criação no failover.

Por padrão, quando você está habilitando a replicação, Site Recovery seleciona todas as interfaces de rede detectadas no servidor local. Ele marca um como **primário** e todos os outros como **secundários**. Todas as interfaces subsequentes adicionadas no servidor local são marcadas como **não usar** por padrão. Quando você estiver adicionando mais interfaces de rede, verifique se o tamanho correto de destino da máquina virtual do Azure está selecionado para acomodar todas as interfaces de rede necessárias.

## <a name="modify-network-interface-settings"></a>Modificar configurações de interface de rede

Você pode modificar a sub-rede e o endereço IP para as interfaces de rede de um item replicado. Se um endereço IP não for especificado, Site Recovery atribuirá o próximo endereço IP disponível da sub-rede para a interface de rede no failover.

1. Selecione qualquer interface de rede disponível para abrir as configurações de interface de rede.

2. Escolha a sub-rede desejada na lista de sub-redes disponíveis.

3. Insira o endereço IP desejado (conforme necessário).

    ![Configurações de interface de rede](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Selecione **OK** para concluir a edição e retornar para o painel **computação e rede** .

5. Repita as etapas 1-4 para outras interfaces de rede.

6. Selecione **salvar** para salvar todas as alterações.

## <a name="next-steps"></a>Passos seguintes
  [Saiba mais](../virtual-network/virtual-network-network-interface-vm.md) sobre interfaces de rede para máquinas virtuais do Azure.
