---
title: Configurar uma rede virtual no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como configurar uma rede virtual existente e a sub-rede e usá-los numa VM com o Azure DevTest Labs
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 8fb3b4ac748fcae2e3aad5b3bfb2a893340dc61a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694817"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurar uma rede virtual no Azure DevTest Labs
Conforme explicado no artigo [adicionar uma VM a um laboratório](devtest-lab-add-vm.md), quando criar uma VM num laboratório, pode especificar uma rede virtual configurada. Por exemplo, poderá ter de aceder aos recursos da rede empresarial suas VMS com a rede virtual que foi configurada com o ExpressRoute ou VPN de site a site.

Este artigo explica como adicionar a sua rede virtual existente para as definições de rede Virtual de um laboratório para que fique disponível para escolher ao criar VMs.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurar uma rede virtual para um laboratório no portal do Azure
Os seguintes passos guiá-lo por meio de adicionar uma rede virtual existente (e a sub-rede) a um laboratório para que possa ser utilizado ao criar uma VM no laboratório do mesmo. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório pretendido. 
1. No painel de principal do laboratório, selecione **Konfigurace a zásady**.

    ![O laboratório configuração e políticas de acesso](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Na **recursos EXTERNOS** secção, selecione **redes virtuais**. É apresentada uma lista de redes virtuais configurados para o laboratório atual, bem como a rede virtual predefinida criada para o laboratório. 
1. Selecione **+ Adicionar**.
   
    ![Adicionar uma rede virtual existente para o laboratório](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Sobre o **rede Virtual** painel, selecione **[selecione a rede virtual]**.
   
    ![Selecione uma rede virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Sobre o **escolher rede virtual** painel, selecione a rede virtual pretendida. É apresentada uma lista que mostra todas as redes virtuais que estão na mesma região na subscrição que o laboratório.
1. Depois de selecionar uma rede virtual, é reencaminhado para o **rede Virtual** painel. Na lista na parte inferior, selecione a sub-rede.

    ![Lista de sub-redes](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    É apresentado o painel sub-rede de laboratório.

    ![Painel de sub-rede de laboratório](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Especifique um **nome da sub-rede de laboratório**.
   - Para permitir uma sub-rede a utilizar na criação da VM de laboratório, selecione **utilização na criação da máquina virtual**.
   - Para ativar uma [partilhado do endereço IP público](devtest-lab-shared-ip.md), selecione **Enable partilhado IP público**.
   - Para permitir que os endereços IP públicos numa sub-rede, selecione **permitir a criação de IP pública**.
   - Na **máquinas de virtuais de máximo por utilizador** campo, especifique as VMs máximas por utilizador para cada sub-rede. Se pretender que um número sem restrições de VMs, deixe este campo em branco.
1. Selecione **OK** para fechar o painel sub-rede de laboratório.
1. Selecione **guardar** para fechar o painel de rede Virtual.

Agora que a rede virtual está configurada, pode ser selecionada quando criar uma VM. Para ver como criar uma VM e especifique uma rede virtual, consulte o artigo [adicionar uma VM a um laboratório](devtest-lab-add-vm.md). 

Do Azure [documentação das redes virtuais](https://docs.microsoft.com/azure/virtual-network) fornece mais informações sobre como utilizar VNets, incluindo como configurar e gerir uma VNet e ligá-la à sua rede no local.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos Seguintes
Depois de adicionar a rede virtual pretendida para seu laboratório, a próxima etapa é [adicionar uma VM para o laboratório](devtest-lab-add-vm.md).

