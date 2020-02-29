---
title: Configure endereços IP privados para VMs - Portal Azure
description: Saiba configurar endereços IP privados para máquinas virtuais utilizando o portal Azure.
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
ms.openlocfilehash: b1019b15463a03282c5d1bd8f0a878433d7f488e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199568"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Configure endereços IP privados para uma máquina virtual utilizando o portal Azure

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os seguintes passos da amostra esperam um ambiente simples já criado. Se quiser executar os passos como são apresentados neste documento, primeiro construa o ambiente de teste descrito na [Create a virtual network](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Como criar um VM para testar endereços IP privados estáticos
Não é possível definir um endereço IP privado estático durante a criação de um VM no modo de implementação do Gestor de Recursos utilizando o portal Azure. Primeiro, tem de criar o VM e, em seguida, definir o seu IP privado para ser estático.

Para criar um VM chamado *DNS01* na subnet *FrontEnd* de uma VNet chamada *TestVNet,* siga estes passos:

1. Num browser, navegue para https://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique **em Criar um recurso** > **Compute** > **Windows Server 2012 R2 Datacenter,** note que a lista **de modelos** de implementação já mostra O Gestor de **Recursos**, e depois clique em **Criar**, como visto na figura seguinte.
   
    ![Criar VM no portal Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. No painel **Basics,** insira o nome do VM para criar *(DNS01* no cenário), a conta de administrador local, e palavra-passe, como visto na figura seguinte.
   
    ![Painel básico](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Certifique-se de que a **Localização** selecionada é *Central US*, depois clique em **Selecionar existente** sob o grupo **Derecursos,** em seguida, clique no **grupo Derecursos** novamente, em seguida, clique em *TestRG*, e, em seguida, clique **EM OK**.
   
    ![Painel básico](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. No painel **De tamanho Escolha um tamanho,** selecione **A1 Standard**, e, em seguida, clique em **Selecionar**.
   
    ![Escolha uma vidraça de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. No painel **Definições,** certifique-se de que as propriedades estão definidas com os seguintes valores e, em seguida, clique EM **OK**.
   
    conta de **armazenamento**-: *armazenamento de vnet*
   
   * **Rede**: *TestVNet*
   * **Subnet**: *FrontEnd*
     
     ![Escolha uma vidraça de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. No painel **resumo,** clique **OK**. Repare no seguinte azulejo exibido no seu painel de instrumentos.
   
    ![Criar VM no portal Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Recomenda-se que não atribua estáticamente o IP privado atribuído à máquina virtual Azure dentro do sistema operativo de um VM, a menos que seja necessário, como quando [atribuir vários endereços IP a um VM windows](virtual-network-multiple-ip-addresses-portal.md). Se configurar manualmente o endereço IP privado dentro do sistema operativo, certifique-se de que é o mesmo endereço do endereço IP privado atribuído à interface de [rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure, ou pode perder a conectividade com a máquina virtual. Saiba mais sobre as definições privadas de [endereço IP.](virtual-network-network-interface-addresses.md#private) Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter estáticas informações de endereço IP privadas para uma VM
Para visualizar as informações estáticas de endereço ip privado para o VM criados com os passos acima, execute os seguintes passos.

1. A partir do portal Azure, clique em **BROWSE ALL** > **Máquinas virtuais** > **DNS01** > **Todas as definições** > **interfaces de rede** e, em seguida, clique na única interface de rede listada.
   
    ![Implantação de azulejos VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. No painel de interface da **Rede,** clique em **todas as definições** > **endereços IP** e note os valores de **endereço de Atribuição** e **IP.**
   
    ![Implantação de azulejos VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a um VM existente
Para adicionar um endereço IP privado estático ao VM criado utilizando os passos acima, siga estes passos:

1. A partir do painel de **endereços IP** acima indicado, clique **em Static** under **Assignment**.
2. Tipo *192.168.1.101* para **endereço IP,** e, em seguida, clique em **Guardar**.
   
    ![Criar VM no portal Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Se depois de clicar em **Guardar,** note que a atribuição ainda está definida para **Dynamic,** significa que o endereço IP que digitou já está a ser utilizado. Experimente um endereço IP diferente.
> 
> 

Recomenda-se que não atribua estáticamente o IP privado atribuído à máquina virtual Azure dentro do sistema operativo de um VM, a menos que seja necessário, como quando [atribuir vários endereços IP a um VM windows](virtual-network-multiple-ip-addresses-portal.md). Se configurar manualmente o endereço IP privado dentro do sistema operativo, certifique-se de que é o mesmo endereço do endereço IP privado atribuído à interface de [rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure, ou pode perder a conectividade com a máquina virtual. Saiba mais sobre as definições privadas de [endereço IP.](virtual-network-network-interface-addresses.md#private) Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático a partir de uma VM
Para remover o endereço IP privado estático do VM acima criado, complete o seguinte passo:

A partir do painel de **endereços IP** acima mostrado, clique em **Dynamic** under **Assignment**, e, em seguida, clique em **Guardar**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Conjunto de endereços IP no sistema operativo

Recomenda-se que não atribua estáticamente o IP privado atribuído à máquina virtual Azure dentro do sistema operativo de um VM, a menos que seja necessário, como quando [atribuir vários endereços IP a um VM windows](virtual-network-multiple-ip-addresses-portal.md). Se configurar manualmente o endereço IP privado dentro do sistema operativo, certifique-se de que é o mesmo endereço do endereço IP privado atribuído à interface de [rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure, ou pode perder a conectividade com a máquina virtual. Saiba mais sobre as definições privadas de [endereço IP.](virtual-network-network-interface-addresses.md#private) Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a gestão das definições de [endereçoIP](virtual-network-network-interface-addresses.md).

