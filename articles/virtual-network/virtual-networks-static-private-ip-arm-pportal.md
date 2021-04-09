---
title: Configurar endereços IP privados para VMs - Portal Azure
description: Saiba como configurar endereços IP privados para máquinas virtuais utilizando o portal Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015949"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Configure um endereço IP privado para um VM utilizando o portal Azure

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os seguintes passos de amostra esperam que um ambiente simples já seja criado. Se pretender executar os passos tal como são apresentados neste documento, crie primeiro [uma rede virtual](quick-create-portal.md#create-a-virtual-network). No entanto, no passo 3, utilize estes valores:

| Definição | Valor |
| ------- | ----- |
| Nome | *TestVNet* |
| Espaço de endereços | *192.168.0.0/16* |
| Grupo de recursos | **TestRG** (se necessário, **selecione Criar novo** para criá-lo) |
| Sub-rede - Nome | *FrontEnd* |
| Sub-rede - Intervalo de endereços | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Criar um VM para testar endereços IP estáticos privados
Quando cria um VM no modo de implementação do Gestor de Recursos, não é possível definir um endereço IP estático privado utilizando o portal Azure. Em vez disso, cria-se primeiro o VM. Em seguida, pode definir o seu IP privado para ser estático.

Para criar um VM chamado *DNS01* na sub-rede *FrontEnd* de uma rede virtual chamada *TestVNet,* siga estes passos:

1. A partir do menu [do portal Azure,](https://portal.azure.com) selecione **Criar um recurso**.

    ![Criar um recurso, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. **Selecione máquina** virtual  >  **compute**.

    ![Criar VM, portal Azure](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. Em **Basics**, especifique os valores dos itens descritos no quadro seguinte. Em seguida, selecione **Seguinte &nbsp; : &nbsp; Discos** e, em **&nbsp; seguida, seguinte : &nbsp; Networking**.

    | Item | Valor |
    | --- | --- |
    | **Subscrição** | A sua subscrição atual |
    | **Grupo de recursos** | **TestRG** (selecione da lista de abandono) |
    | **Nome da máquina virtual** | *DNS01* |
    | **Região** | **(EUA) E.U.A Leste** |
    | **Imagem** | **Windows Server 2019 Datacenter** |
    | **Tamanho** | **Tamanho VM** de **B1ls,** **Oferta** de **Standard** |
    | **Nome de Utilizador** | O nome de utilizador da sua conta de administrador |
    | **Palavra-passe** | A palavra-passe para o nome de utilizador da sua conta de administrador |
    | **Confirmar palavra-passe** | A senha de novo |

    ![Separador básico, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. Em **Networking**, especifique os valores dos itens descritos na tabela seguinte e, em seguida, selecione **Seguinte**.

    | Item | Valor |
    | --- | --- |
    | **Rede virtual** | **TestVNet** |
    | **Sub-rede** | **FrontEnd** |

    ![Separador de rede, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. Em **Gestão**, na **conta de armazenamento de Diagnóstico,** escolha **vnetstorage**. Se essa conta de armazenamento não aparecer na lista, selecione **Criar novo,** especifique um **Nome** de *vnetstorage*, e selecione **OK**. Finalmente, **selecione 'Revisão' &nbsp; + &nbsp; criar**.

    ![Separador de gestão, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Em **Rever + criar,** rever as informações gerais e, em seguida, selecionar **Criar**.

    ![Rever + criar separador, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

A seguinte mensagem aparece assim que o VM é criado.

![Mensagem de conclusão de implementação, Criar uma máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Recuperar informações privadas de endereço IP para um VM
Para ver as informações privadas do endereço IP para o seu novo VM:

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar o seu VM. Procure e selecione **máquinas Virtuais.**

    ![Máquinas virtuais, caixa de pesquisa, portal Azure](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Selecione o nome do seu novo VM **(DNS01).**

    ![Lista de máquinas virtuais, portal Azure](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Escolha **o Networking** e selecione a única interface de rede listada.

    ![Interface de rede, networking, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Escolha **as configurações IP** e selecione a configuração IP listada na tabela.

    ![Configuração IP, Interface de rede, networking, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. Nas **definições de endereços IP privados**, sob a rede virtual **TestVNet/FrontEnd,** note o valor **de atribuição** **(Dinâmico** ou **Estático)** e o **endereço IP**.

    ![Atribuição dinâmica ou estática, definições de endereço IP privados antigos, configuração IP, interface de rede, rede, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Adicione um endereço IP estático privado a um VM existente
Para adicionar um endereço IP estático privado ao seu novo VM:

1. Na página de configuração IP, desaponsee a atribuição do seu endereço IP privado para **Estática**.
2. Mude o **seu endereço IP** privado para *192.168.1.101* e, em seguida, selecione **Guardar**.
   
    ![Atribuição dinâmica ou estática, novas definições de endereços IP privados, configuração IP, interface de rede, rede, máquina virtual, portal Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Se notar depois de selecionar **Guarde** que a atribuição ainda está definida para **Dynamic,** o endereço IP que escreveu já está em uso. Tente outro endereço IP.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Remova um endereço IP estático privado de um VM
Para remover o endereço IP privado estático do seu VM:

Na página de configuração IP, desacione a atribuição do seu endereço IP privado para **Dynamic** e, em seguida, selecione **Guardar**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Definir endereços IP dentro do sistema operativo

De dentro do sistema operativo de um VM, não deve atribuir estáticamente o IP *privado* que é atribuído ao VM Azure. Só faz a atribuição estática de um IP privado quando é necessário, como quando [atribuir muitos endereços IP a VMs](virtual-network-multiple-ip-addresses-portal.md). Se definir manualmente o endereço IP privado dentro do sistema operativo, certifique-se de que corresponde ao endereço IP privado atribuído à [interface de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure . Caso contrário, pode perder conectividade com o VM. Saiba mais sobre as definições [privadas de endereço IP.](virtual-network-network-interface-addresses.md#private)

Além disso, nunca deve atribuir manualmente o endereço IP *público* atribuído a uma máquina virtual Azure dentro do sistema operativo da máquina virtual.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a gestão das [definições de endereço IP](virtual-network-network-interface-addresses.md).
