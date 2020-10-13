---
title: Configure uma rede virtual em Azure DevTest Labs Microsoft Docs
description: Aprenda a configurar uma rede virtual e sub-rede existentes, e use-as num VM com Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 21fe23b6be6fdc924c5502ff97f9c03a3f3e759d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87283559"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurar uma rede virtual em Azure DevTest Labs
Como explicado no artigo [Adicionar um VM a um laboratório](devtest-lab-add-vm.md), quando criar um VM em um laboratório, você pode especificar uma rede virtual configurada. Por exemplo, poderá ter de aceder aos recursos da sua rede a partir dos seus VMs utilizando a rede virtual configurada com o ExpressRoute ou a VPN site-to-site.

Este artigo explica como adicionar a sua rede virtual existente nas definições de Rede Virtual de um laboratório para que esteja disponível para escolher ao criar VMs.

> [!NOTE]
> Para saber mais sobre os custos associados ao serviço Azure Virtual Network, consulte [preços para a Rede Virtual Azure.](../virtual-network/virtual-networks-overview.md#pricing)

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurar uma rede virtual para um laboratório usando o portal Azure
Os passos seguintes acompanham-no através da adição de uma rede virtual existente (e sub-rede) a um laboratório para que possa ser usado ao criar um VM no mesmo laboratório. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o laboratório desejado. 
1. No painel principal do laboratório, **selecione Configuração e políticas**.

    ![Aceda à configuração e políticas do laboratório](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Na secção **RECURSOS EXTERNOS,** selecione **redes Virtuais.** É apresentada uma lista de redes virtuais configuradas para o laboratório atual, bem como a rede virtual padrão criada para o seu laboratório. 
1. Selecione **+ Adicionar**.
   
    ![Adicione uma rede virtual existente ao seu laboratório](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. No **painel de rede Virtual,** selecione **[Selecione rede virtual]**.
   
    ![Selecione uma rede virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. No painel **de rede virtual Escolha,** selecione a rede virtual desejada. É apresentada uma lista que mostra todas as redes virtuais que estão sob a mesma região na subscrição que o laboratório.
1. Depois de selecionar uma rede virtual, é devolvido ao painel **de rede Virtual.** Selecione a sub-rede na lista na parte inferior.

    ![Lista de sub-redes](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    É apresentado o painel de sub-rede lab.

    ![Painel de sub-rede de laboratório](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Especificar um **nome de sub-rede lab**.
   - Para permitir a utilização de uma sub-rede na criação de VM em laboratório, selecione **Use in virtual machine creation**.
   - Para ativar um [endereço IP público partilhado,](devtest-lab-shared-ip.md)selecione **Enable shared public IP**.
   - Para permitir endereços IP públicos numa sub-rede, selecione **Permitir a criação de IP público**.
   - Nas **máquinas virtuais máximas por** campo de utilizador, especifique os VMs máximos por utilizador para cada sub-rede. Se quiser um número ilimitado de VMs, deixe este campo em branco.
1. Selecione **OK** para fechar o painel de sub-rede de laboratório.
1. **Selecione Guardar** para fechar o painel de rede Virtual.

Agora que a rede virtual está configurada, pode ser selecionada ao criar um VM. Para ver como criar um VM e especificar uma rede virtual, consulte o artigo, [Adicione um VM a um laboratório](devtest-lab-add-vm.md). 

[A Documentação](../virtual-network/index.yml) da Rede Virtual do Azure fornece mais informações sobre como usar VNets, incluindo como configurar e gerir um VNet e conectá-lo à sua rede no local.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de ter adicionado a rede virtual desejada ao seu laboratório, o próximo passo é [adicionar um VM ao seu laboratório.](devtest-lab-add-vm.md)
