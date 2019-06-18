---
title: Gerir as interfaces de rede no Azure Site Recovery para recuperação de desastre no local para o Azure | Documentos da Microsoft
description: Descreve como gerir as interfaces de rede para recuperação de desastre no local para o Azure com o Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 5d5dd7bc3f6b60c2f9d7c2179f2bd356ca101dc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61471779"
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Gerir as interfaces de rede de máquina virtual para recuperação de desastre no local para o Azure
Uma máquina virtual (VM) no Azure tem de ter, pelo menos, uma interface de rede ligada ao mesmo. Ele pode ter como muitos anexados a ele como o suporte de tamanho VM de interfaces de rede.

Por predefinição, a primeira interface de rede ligada a uma máquina virtual do Azure é definida como a interface de rede principal. Todas as outras interfaces de rede na máquina virtual são interfaces de rede secundárias. Também por predefinição, todo o tráfego de saída da máquina virtual é enviado o endereço IP que está atribuído a configuração de IP principal da interface de rede principal.

Num ambiente no local, máquinas virtuais ou servidores podem ter várias interfaces de rede para redes diferentes dentro do ambiente. Redes diferentes são normalmente utilizadas para a execução de operações específicas, tais como atualizações, manutenção e acesso à internet. Quando está migrando ou fazer failover para o Azure a partir de um ambiente no local, tenha em atenção que as interfaces de rede na mesma máquina virtual devem todas estar ligadas à mesma rede virtual.

Por predefinição, o Azure Site Recovery cria como interfaces numa máquina virtual do Azure de rede estão ligados ao servidor no local. Pode evitar a criação de interfaces de rede redundantes durante a migração ou a ativação pós-falha ao editar as definições de interface de rede nas definições para a máquina virtual replicada.

## <a name="select-the-target-network"></a>Selecione a rede de destino

Para o VMware e máquinas físicas e para máquinas de virtuais de Hyper-V (sem System Center Virtual Machine Manager), pode especificar a rede virtual de destino para máquinas virtuais individuais. Para máquinas de virtuais de Hyper-V gerenciadas com o Virtual Machine Manager, utilize [mapeamento da rede](site-recovery-network-mapping.md) para mapear as redes VM num servidor de origem do Virtual Machine Manager e redes do Azure de destino.

1. Sob **itens replicados** num cofre dos serviços de recuperação, selecione qualquer item replicado para aceder às definições do item replicado.

2. Selecione o **computação e rede** separador para acessar as configurações de rede para o item replicado.

3. Sob **propriedades de rede**, escolha uma rede virtual a partir da lista de interfaces de rede disponíveis.

    ![Definições de rede](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Modificar a rede de destino afeta todas as interfaces de rede para que a máquina virtual específica.

Para as nuvens do Virtual Machine Manager, a modificação de mapeamento de rede afeta todas as máquinas virtuais e suas interfaces de rede.

## <a name="select-the-target-interface-type"></a>Selecione o tipo de interface de destino

Sob o **interfaces de rede** secção a **computação e rede** painel, pode ver e editar as definições de interface de rede. Também pode especificar o tipo de interface de rede de destino.

- R **primário** interface de rede é necessária para a ativação pós-falha.
- Interfaces de todos os outro rede selecionada, se houver, são **secundário** interfaces de rede.
- Selecione **não use** para excluir uma interface de rede da criação, a ativação pós-falha.

Por predefinição, quando estiver a ativar a replicação, o Site Recovery seleciona todas as interfaces de rede detetados no servidor no local. Marca um como **primário** e todos os outros como **secundário**. Qualquer interface subsequente, adicionado no servidor no local é marcado **não use** por predefinição. Quando estiver a adicionar mais interfaces de rede, certifique-se de que o tamanho de destino da máquina virtual do Azure correta está selecionado para acomodar todas as interfaces de rede necessária.

## <a name="modify-network-interface-settings"></a>Modificar as definições de interface de rede

É possível modificar a sub-rede e o endereço IP para interfaces de rede de um item replicado. Se não for especificado um endereço IP, Site Recovery atribuir o endereço IP disponível seguinte a partir da sub-rede para a interface de rede a ativação pós-falha.

1. Selecione qualquer interface de rede disponível para abrir as definições de interface de rede.

2. Escolha a sub-rede pretendida na lista de sub-redes disponíveis.

3. Introduza o endereço IP pretendido (conforme necessário).

    ![Definições de interface de rede](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Selecione **OK** para concluir a edição e voltar para o **computação e rede** painel.

5. Repita os passos 1 a 4 para outras interfaces de rede.

6. Selecione **guardar** para guardar todas as alterações.

## <a name="next-steps"></a>Passos Seguintes
  [Saiba mais](../virtual-network/virtual-network-network-interface-vm.md) sobre as interfaces de rede para máquinas virtuais do Azure.
