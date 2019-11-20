---
title: Configurar endereços IP privados para VMs-portal do Azure
description: Saiba como configurar endereços IP privados para máquinas virtuais usando o portal do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: bd734f171f4e10c4227fbab77485a788f02848b3
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196641"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Configurar endereços IP privados para uma máquina virtual usando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [CLI do Azure](virtual-networks-static-private-ip-arm-cli.md)
> * [Portal do Azure (clássico)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (Clássico)](virtual-networks-static-private-ip-classic-ps.md)
> * [CLI do Azure (clássico)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação do Resource Manager. Você também pode [gerenciar o endereço IP privado estático no modelo de implantação clássico](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de exemplo a seguir esperam um ambiente simples já criado. Se você quiser executar as etapas conforme elas são exibidas neste documento, primeiro crie o ambiente de teste descrito em [criar uma rede virtual](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Como criar uma VM para testar endereços IP privados estáticos
Você não pode definir um endereço IP privado estático durante a criação de uma VM no modo de implantação do Gerenciador de recursos usando o portal do Azure. Você deve criar a VM primeiro e, em seguida, definir seu IP privado como estático.

Para criar uma VM denominada *DNS01* na sub-rede *frontend* de uma VNet denominada *TestVNet*, siga estas etapas:

1. Num browser, navegue para https://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **criar um recurso** > **computação** > **Windows Server 2012 R2 Datacenter**, observe que a lista **selecionar um modelo de implantação** já mostra o **Gerenciador de recursos**e, em seguida, clique em **criar**, conforme mostrado na figura a seguir.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. No painel **básico** , insira o nome da VM a ser criada (*DNS01* no cenário), a conta de administrador local e a senha, conforme mostrado na figura a seguir.
   
    ![Painel básico](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Verifique se o **local** selecionado está *EUA Central*, em seguida, clique em **selecionar existente** em **grupo de recursos**, clique em **grupo de recursos** novamente, clique em *TestRG*e em **OK**.
   
    ![Painel básico](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. No painel **escolher um tamanho** , selecione **padrão a1**e clique em **selecionar**.
   
    ![Escolher um painel de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. No painel **configurações** , verifique se as propriedades estão definidas com os valores a seguir e clique em **OK**.
   
    -**conta de armazenamento**: *vnetstorage*
   
   * **Rede**: *TestVNet*
   * **Sub-rede**: *frontend*
     
     ![Escolher um painel de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. No painel **Resumo** , clique em **OK**. Observe o bloco a seguir exibido no painel.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

É recomendável que você não atribua estaticamente o IP privado atribuído à máquina virtual do Azure no sistema operacional de uma VM, a menos que seja necessário, como ao [atribuir vários endereços IP a uma VM do Windows](virtual-network-multiple-ip-addresses-portal.md). Se você definir manualmente o endereço IP privado no sistema operacional, verifique se ele é o mesmo endereço que o endereço IP privado atribuído ao [adaptador de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)do Azure ou se você pode perder a conectividade com a máquina virtual. Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md#private) . Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter estáticas informações de endereço IP privadas para uma VM
Para exibir as informações de endereço IP privado estático para a VM criada com as etapas acima, execute as etapas a seguir.

1. Na portal do Azure, clique em **Procurar tudo** > **máquinas virtuais** > **DNS01** > **todas as configurações** > **interfaces de rede** e, em seguida, clique no único adaptador de rede listado.
   
    ![Implantando bloco de VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. No painel **interface de rede** , clique em **todas as configurações** > **endereços IP** e observe os valores de **atribuição** e **endereço IP** .
   
    ![Implantando bloco de VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a uma VM existente
Para adicionar um endereço IP privado estático à VM criada usando as etapas acima, siga estas etapas:

1. No painel **endereços IP** mostrado acima, clique em **estático** em **atribuição**.
2. Digite *192.168.1.101* para **endereço IP**e clique em **salvar**.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Se, depois de clicar em **salvar**, você observar que a atribuição ainda está definida como **dinâmica**, isso significa que o endereço IP digitado já está em uso. Tente um endereço IP diferente.
> 
> 

É recomendável que você não atribua estaticamente o IP privado atribuído à máquina virtual do Azure no sistema operacional de uma VM, a menos que seja necessário, como ao [atribuir vários endereços IP a uma VM do Windows](virtual-network-multiple-ip-addresses-portal.md). Se você definir manualmente o endereço IP privado no sistema operacional, verifique se ele é o mesmo endereço que o endereço IP privado atribuído ao [adaptador de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)do Azure ou se você pode perder a conectividade com a máquina virtual. Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md#private) . Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático a partir de uma VM
Para remover o endereço IP privado estático da VM criada acima, conclua a seguinte etapa:

No painel **endereços IP** mostrado acima, clique em **dinâmico** em **atribuição**e, em seguida, clique em **salvar**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Conjunto de endereços IP no sistema operativo

É recomendável que você não atribua estaticamente o IP privado atribuído à máquina virtual do Azure no sistema operacional de uma VM, a menos que seja necessário, como ao [atribuir vários endereços IP a uma VM do Windows](virtual-network-multiple-ip-addresses-portal.md). Se você definir manualmente o endereço IP privado no sistema operacional, verifique se ele é o mesmo endereço que o endereço IP privado atribuído ao [adaptador de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)do Azure ou se você pode perder a conectividade com a máquina virtual. Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md#private) . Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o gerenciamento de [configurações de endereço IP](virtual-network-network-interface-addresses.md).

