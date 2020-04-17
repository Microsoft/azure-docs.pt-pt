---
title: Configure endereços IP privados para VMs - Portal Azure
description: Saiba configurar endereços IP privados para máquinas virtuais utilizando o portal Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: 946926a8a805ec3c53ea3c57dc3eded2462f7673
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461553"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Configure um endereço IP privado para um VM utilizando o portal Azure

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os seguintes passos da amostra esperam que já seja criado um ambiente simples. Se quiser executar os passos como são apresentados neste documento, [crie](quick-create-portal.md#create-a-virtual-network)primeiro uma rede virtual . No entanto, na etapa 3, utilize estes valores:

| Definição | Valor |
| ------- | ----- |
| Nome | *TestVNet* |
| Espaço de endereços | *192.168.0.0/16* |
| Grupo de recursos | **TestRG** (se necessário, selecione **Criar novo** para criá-lo) |
| Subnet - Nome | *FrontEnd* |
| Sub-rede - Intervalo de endereços | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Criar um VM para testar endereços IP privados estáticos
Ao criar um VM no modo de implementação do Gestor de Recursos, não é possível definir um endereço IP privado estático utilizando o portal Azure. Em vez disso, cria-se primeiro o VM. Então pode definir o seu IP privado para ser estático.

Para criar um VM chamado *DNS01* na subnet *FrontEnd* de uma rede virtual chamada *TestVNet,* siga estes passos:

1. A partir do [menu do portal Azure,](https://portal.azure.com) selecione **Criar um recurso**.

    ![Criar um recurso, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Selecione Máquina**Virtual** **Compute** > .

    ![Criar VM, portal Azure](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. No **Basics,** especifique valores para itens descritos no quadro seguinte. Em seguida, selecione **&nbsp;&nbsp;Seguinte: Discos** e, em **seguida,&nbsp;Seguinte:&nbsp;Networking**.

    | Item | Valor |
    | --- | --- |
    | **Subscrição** | A sua subscrição atual |
    | **Grupo de recursos** | **TestRG** (selecione na lista de dropdown) |
    | **Nome da máquina virtual** | *DNS01* |
    | **Região** | **(EUA) Leste dos EUA** |
    | **Imagem** | **Windows Server 2019 Datacenter** |
    | **Tamanho** | **Tamanho VM** de **B1ls,** **Oferta** de **Padrão** |
    | **Nome de utilizador** | O nome de utilizador da sua conta de administrador |
    | **Palavra-passe** | A palavra-passe para o nome de utilizador da sua conta de administrador |
    | **Confirmar palavra-passe** | A senha de novo |

    ![Separador basics, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. Em **Rede,** especifique valores para itens descritos na tabela seguinte e, em seguida, selecione **Seguinte**.

    | Item | Valor |
    | --- | --- |
    | **Rede virtual** | **TestVNet** |
    | **Sub-rede** | **FrontEnd** |

    ![Separador de rede, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. Em **Gestão,** sob a conta de **armazenamento de Diagnósticos,** escolha o **vnetstorage.** Se essa conta de armazenamento não aparecer na lista, selecione **Criar novo,** especificar um **nome** de armazenamento de *vnet,* e selecione **OK**. Finalmente, selecione **Review&nbsp;+&nbsp;criar**.

    ![Separador de gestão, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Em **Review + criar**, reveja a informação sobre visão geral e, em seguida, selecione **Criar**.

    ![Rever + criar separador, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

A seguinte mensagem aparece assim que o VM é criado.

![Mensagem de conclusão de implantação, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Recuperar informações privadas de endereço IP para um VM
Para ver as informações privadas de endereço IP para o seu novo VM:

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar o seu VM. Procure e selecione **máquinas Virtuais**.

    ![Máquinas virtuais, caixa de pesquisa, portal Azure](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Selecione o nome do seu novo VM **(DNS01**).

    ![Lista de máquinas virtuais, portal Azure](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Escolha **networking**e selecione a única interface de rede listada.

    ![Interface de rede, networking, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Escolha **as configurações IP**e selecione a configuração IP listada na tabela.

    ![Configuração IP, Interface de rede, networking, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. Nas **definições de endereço IP privados,** sob a rede virtual **TestVNet/FrontEnd/subnet,** note o valor **de atribuição** **(Dinâmico** ou **Estático)** e o **endereço IP**.

    ![Atribuição dinâmica ou estática, definições antigas de endereçoIP privado, configuração IP, Interface de rede, networking, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Adicione um endereço IP privado estático a um VM existente
Para adicionar um endereço IP privado estático ao seu novo VM:

1. Na página de configuração IP, delineie a atribuição do seu endereço IP privado para **Static**.
2. Altere o seu **endereço IP** privado para *192.168.1.101*e, em seguida, selecione **Guardar**.
   
    ![Atribuição dinâmica ou estática, novas definições de endereçoIP privados, configuração IP, Interface de rede, networking, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Se notar depois de selecionar **Guardar** que a atribuição ainda está definida para **Dynamic,** o endereço IP que digitou já está a ser utilizado. Tente outro endereço IP.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Remova um endereço IP privado estático de um VM
Para remover o endereço IP privado estático do seu VM:

Na página de configuração IP, delineie a atribuição para o seu endereço IP privado para **Dynamic**, e, em seguida, selecione **Guardar**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Definir endereços IP dentro do sistema operativo

A partir do sistema operativo de um VM, não deve atribuir o IP *privado* atribuído ao VM Azure. Faça apenas a atribuição estática de um IP privado quando é necessário, como quando [atribuir muitos endereços IP a VMs](virtual-network-multiple-ip-addresses-portal.md). Se definir manualmente o endereço IP privado dentro do sistema operativo, certifique-se de que corresponde ao endereço IP privado atribuído à interface de [rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure . Caso contrário, pode perder a conectividade com o VM. Saiba mais sobre as definições privadas de [endereço IP.](virtual-network-network-interface-addresses.md#private)

Além disso, nunca deve atribuir manualmente o endereço IP *público* atribuído a uma máquina virtual Azure dentro do sistema operativo da máquina virtual.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a gestão das definições de [endereçoIP](virtual-network-network-interface-addresses.md).
