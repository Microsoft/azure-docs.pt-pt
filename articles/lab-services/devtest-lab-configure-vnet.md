---
title: Configurar uma rede virtual no Azure DevTest Labs | Microsoft Docs
description: Saiba como configurar uma rede virtual e uma sub-rede existentes e usá-las em uma VM com Azure DevTest Labs
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
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390038"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurar uma rede virtual no Azure DevTest Labs
Conforme explicado no artigo [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md), ao criar uma VM em um laboratório, você pode especificar uma rede virtual configurada. Por exemplo, talvez seja necessário acessar os recursos do corpnet de suas VMs usando a rede virtual que foi configurada com o ExpressRoute ou VPN site a site.

Este artigo explica como adicionar sua rede virtual existente nas configurações de rede virtual de um laboratório para que ela esteja disponível para escolha ao criar VMs.

> [!NOTE]
> Para saber mais sobre os custos associados ao serviço de rede virtual do Azure, consulte [preços da rede virtual do Azure](../virtual-network/virtual-networks-overview.md#pricing).

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurar uma rede virtual para um laboratório usando o portal do Azure
As etapas a seguir orientam você pela adição de uma rede virtual (e sub-rede) existente a um laboratório para que ele possa ser usado ao criar uma VM no mesmo laboratório. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
1. Na lista de laboratórios, selecione o laboratório desejado. 
1. No painel principal do laboratório, selecione **configuração e políticas**.

    ![Acessar a configuração e as políticas do laboratório](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Na seção **recursos externos** , selecione **redes virtuais**. Uma lista de redes virtuais configuradas para o laboratório atual é exibida, bem como a rede virtual padrão criada para seu laboratório. 
1. Selecione **+ Adicionar**.
   
    ![Adicionar uma rede virtual existente ao seu laboratório](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. No painel **rede virtual** , selecione **[selecionar rede virtual]** .
   
    ![Selecionar uma rede virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. No painel **escolher rede virtual** , selecione a rede virtual desejada. É exibida uma lista mostrando todas as redes virtuais que estão na mesma região na assinatura que o laboratório.
1. Depois de selecionar uma rede virtual, você retornará ao painel de **rede virtual** . Selecione a sub-rede na lista na parte inferior.

    ![Lista de sub-redes](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    O painel de sub-rede do laboratório é exibido.

    ![Painel de sub-rede do laboratório](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Especifique um **nome de sub-rede do laboratório**.
   - Para permitir que uma sub-rede seja usada na criação da VM do laboratório, selecione **usar na criação da máquina virtual**.
   - Para habilitar um [endereço IP público compartilhado](devtest-lab-shared-ip.md), selecione **habilitar IP público compartilhado**.
   - Para permitir endereços IP públicos em uma sub-rede, selecione **permitir criação de IP público**.
   - No campo **máximo de máquinas virtuais por usuário** , especifique o máximo de VMs por usuário para cada sub-rede. Se você quiser um número irrestrito de VMs, deixe esse campo em branco.
1. Selecione **OK** para fechar o painel de sub-rede do laboratório.
1. Selecione **salvar** para fechar o painel rede virtual.

Agora que a rede virtual está configurada, ela pode ser selecionada durante a criação de uma VM. Para ver como criar uma VM e especificar uma rede virtual, consulte o artigo [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md). 

A [documentação de rede virtual](https://docs.microsoft.com/azure/virtual-network) do Azure fornece mais informações sobre como usar o VNets, incluindo como configurar e gerenciar uma VNet e conectá-la à sua rede local.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de ter adicionado a rede virtual desejada ao seu laboratório, a próxima etapa é [Adicionar uma VM ao seu laboratório](devtest-lab-add-vm.md).

