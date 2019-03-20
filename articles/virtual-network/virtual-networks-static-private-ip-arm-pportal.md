---
title: Configurar endereços IP privados para as VMs - portal do Azure | Documentos da Microsoft
description: Saiba como configurar os endereços IP privados para as máquinas virtuais no portal do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5efe0516d1b2dd387532d31a0a6654e6651fe41
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011732"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Configurar endereços IP privados para uma máquina virtual com o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [CLI do Azure](virtual-networks-static-private-ip-arm-cli.md)
> * [Portal do Azure (clássico)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (Clássico)](virtual-networks-static-private-ip-classic-ps.md)
> * [CLI do Azure (clássico)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação do Resource Manager. Também pode [gerir o endereço IP privado estático no modelo de implementação clássica](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os seguintes passos de exemplo esperam um ambiente simples já criado. Se quiser executar os passos à medida que são apresentadas neste documento, primeiro crie o ambiente de teste descrito em [criar uma rede virtual](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Como criar uma VM para endereços IP privados estáticos de teste
Não é possível definir um endereço IP privado estático durante a criação de uma VM no modo de implementação Resource Manager com o portal do Azure. Tem de criar a VM pela primeira vez, e definir o IP privado estático.

Para criar uma VM com o nome *DNS01* no *front-end* sub-rede de uma VNet com o nome *TestVNet*, siga estes passos:

1. Num browser, navegue para https://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **criar um recurso** > **computação** > **Windows Server 2012 R2 Datacenter**, tenha em atenção que o **Selecione uma implementação modelo** já lista mostra **do Resource Manager**e, em seguida, clique em **criar**, como mostrado na figura a seguir.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. Na **Noções básicas** painel, introduza o nome da VM para criar (*DNS01* no cenário), a conta de administrador local e a palavra-passe, como visto na figura a seguir.
   
    ![Painel Noções básicas](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Certifique-se a **localização** selecionado é *E.U.A. Central*, em seguida, clique em **selecionar existente** em **grupo de recursos**, em seguida, clique em **Grupo de recursos** mais uma vez, em seguida, clique em *TestRG*e, em seguida, clique em **OK**.
   
    ![Painel Noções básicas](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. Na **escolher um tamanho** painel, selecione **A1 padrão**e, em seguida, clique em **selecionar**.
   
    ![Escolha um painel de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. Na **configurações** painel, certifique-se de que as propriedades são definidas com os seguintes valores e, em seguida, clique em **OK**.
   
    -**Conta de armazenamento**: *vnetstorage*
   
   * **Rede**: *TestVNet*
   * **Sub-rede**: *FrontEnd*
     
     ![Escolha um painel de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. Na **resumo** painel, clique em **OK**. Observe o mosaico seguinte, apresentado no seu dashboard.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

É recomendável que não atribuir estaticamente IP privado atribuído à máquina virtual do Azure no sistema operativo de uma VM, a menos que necessário, como quando [atribuição de IP de vários endereços para uma VM do Windows](virtual-network-multiple-ip-addresses-portal.md). Se definir manualmente o endereço IP privado no sistema operativo, certifique-se de que é o mesmo endereço como o endereço IP privado atribuído para o Azure [interface de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings), ou pode perder a conectividade para a máquina virtual. Saiba mais sobre [endereço IP privado](virtual-network-network-interface-addresses.md#private) definições. Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter estáticas informações de endereço IP privadas para uma VM
Para ver as estáticas informações de endereço IP privadas para a VM criada com os passos acima, execute os seguintes passos.

1. A partir do portal do Azure, clique em **Procurar tudo** > **máquinas virtuais** > **DNS01** > **todas as definições**  >  **Interfaces de rede** e, em seguida, clique na interface de rede única listada.
   
    ![Implantando o mosaico VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. Na **interface de rede** painel, clique em **todas as definições** > **endereços IP** e repare que o **atribuição** e  **Endereço IP** valores.
   
    ![Implantando o mosaico VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a uma VM existente
Para adicionar um endereço IP privado estático para a VM criada utilizando os passos acima, siga estes passos:

1. Do **endereços IP** painel mostrado acima, clique em **estático** sob **atribuição**.
2. Tipo *192.168.1.101* para **endereço IP**e, em seguida, clique em **guardar**.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Se depois de clicar em **salvar**, que tenha em atenção que a atribuição ainda está definida como **dinâmico**, significa que o endereço IP que introduziu já está em utilização. Tente um endereço IP diferente.
> 
> 

É recomendável que não atribuir estaticamente IP privado atribuído à máquina virtual do Azure no sistema operativo de uma VM, a menos que necessário, como quando [atribuição de IP de vários endereços para uma VM do Windows](virtual-network-multiple-ip-addresses-portal.md). Se definir manualmente o endereço IP privado no sistema operativo, certifique-se de que é o mesmo endereço como o endereço IP privado atribuído para o Azure [interface de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings), ou pode perder a conectividade para a máquina virtual. Saiba mais sobre [endereço IP privado](virtual-network-network-interface-addresses.md#private) definições. Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático a partir de uma VM
Para remover o endereço IP privado estático da VM criada acima, conclua o passo seguinte:

Do **endereços IP** painel mostrado acima, clique em **dinâmico** sob **atribuição**e, em seguida, clique em **guardar**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Conjunto de endereços IP no sistema operativo

É recomendável que não atribuir estaticamente IP privado atribuído à máquina virtual do Azure no sistema operativo de uma VM, a menos que necessário, como quando [atribuição de IP de vários endereços para uma VM do Windows](virtual-network-multiple-ip-addresses-portal.md). Se definir manualmente o endereço IP privado no sistema operativo, certifique-se de que é o mesmo endereço como o endereço IP privado atribuído para o Azure [interface de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings), ou pode perder a conectividade para a máquina virtual. Saiba mais sobre [endereço IP privado](virtual-network-network-interface-addresses.md#private) definições. Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como gerir [definições do endereço IP](virtual-network-network-interface-addresses.md).

