---
title: Configure uma rede virtual em Azure DevTest Labs [ Laboratórios DevTest] Microsoft Docs
description: Aprenda a configurar uma rede virtual e subnet existentes, e use-os num VM com Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 6cf3d2f82c98a3caab47ff48a600316747932b72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70390038"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configure uma rede virtual em Azure DevTest Labs
Como explicado no artigo [Adicione um VM a um laboratório,](devtest-lab-add-vm.md)quando criar um VM num laboratório, pode especificar uma rede virtual configurada. Por exemplo, poderá ter de aceder aos seus recursos de rede de suporte a partir dos seus VMs utilizando a rede virtual que foi configurada com a ExpressRoute ou vpN site-to-site.

Este artigo explica como adicionar a sua rede virtual existente às definições da Rede Virtual de um laboratório para que esteja disponível para escolher ao criar VMs.

> [!NOTE]
> Para conhecer os custos associados ao serviço de Rede Virtual Azure, consulte [preços para a Rede Virtual Azure.](../virtual-network/virtual-networks-overview.md#pricing)

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configure uma rede virtual para um laboratório usando o portal Azure
Os seguintes passos passam por adicionar uma rede virtual (e subnet) existente a um laboratório para que possa ser usado na criação de um VM no mesmo laboratório. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o laboratório desejado. 
1. No painel principal do laboratório, selecione **Configuração e políticas**.

    ![Aceda à configuração e políticas do laboratório](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Na secção **RECURSOS EXTERNOS,** selecione **redes Virtuais.** Uma lista de redes virtuais configuradas para o laboratório atual é apresentada, bem como a rede virtual padrão criada para o seu laboratório. 
1. Selecione **+ Adicionar**.
   
    ![Adicione uma rede virtual existente ao seu laboratório](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. No painel de **rede Virtual,** selecione **[Selecione rede virtual]**.
   
    ![Selecione uma rede virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. No painel de **rede virtual Choose,** selecione a rede virtual desejada. É apresentada uma lista que mostra todas as redes virtuais que estão sob a mesma região na subscrição que o laboratório.
1. Depois de selecionar uma rede virtual, é devolvido ao painel de **rede Virtual.** Selecione a sub-rede na lista na parte inferior.

    ![Lista de sub-redes](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    O painel de sub-rede do Laboratório é exibido.

    ![Painel de sub-rede de laboratório](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Especifique um nome de **sub-rede lab**.
   - Para permitir que uma subrede seja utilizada na criação de VM em laboratório, selecione **Use na criação de máquinas virtuais**.
   - Para ativar um [endereço IP público partilhado,](devtest-lab-shared-ip.md)selecione **Enable ip público partilhado**.
   - Para permitir endereços IP públicos numa subnet, selecione Permitir a **criação pública de IP**.
   - Nas **máquinas virtuais máximas por** campo de utilizador, especifique os VMs máximos por utilizador para cada sub-rede. Se quiser um número ilimitado de VMs, deixe este campo em branco.
1. Selecione **OK** para fechar o painel de subnet lab.
1. Selecione **Guardar** para fechar o painel de rede Virtual.

Agora que a rede virtual está configurada, pode ser selecionada ao criar um VM. Para ver como criar um VM e especificar uma rede virtual, consulte o artigo, [Adicione um VM a um laboratório](devtest-lab-add-vm.md). 

[A Documentação](https://docs.microsoft.com/azure/virtual-network) de Rede Virtual da Azure fornece mais informações sobre como usar VNets, incluindo como configurar e gerir um VNet e conectá-lo à sua rede no local.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de ter adicionado a rede virtual desejada ao seu laboratório, o próximo passo é [adicionar um VM ao seu laboratório.](devtest-lab-add-vm.md)

