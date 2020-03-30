---
title: Configure endereços IP privados para VMs (Clássico) - Portal Azure / Microsoft Docs
description: Saiba configurar endereços IP privados para máquinas virtuais (Clássicas) utilizando o portal Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9de9fd2eb7a46719b841a885ac173f8d9c1271c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71059113"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configure endereços IP privados para uma máquina virtual (Clássico) utilizando o portal Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [gerir um endereço IP privado estático no modelo](virtual-networks-static-private-ip-arm-pportal.md)de implementação do Gestor de Recursos.

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os passos da amostra que se seguem esperam um ambiente simples já criado. Se quiser executar os passos como são apresentados neste documento, primeiro construa o ambiente de teste descrito na criação de [uma vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar um VM
Para criar um VM chamado *DNS01* na subnet *FrontEnd* de uma VNet chamada *TestVNet* com um IP privado estático de *192.168.1.101,* complete os seguintes passos:

1. Num browser, navegue para https://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Selecione **NOVO** > **Compute** > **Windows Server 2012 R2 Datacenter,** note que a lista de modelos de **implementação já** mostra **Classic**, e depois selecione **Create**.
   
    ![Criar VM no portal Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. No âmbito da **Create VM,** introduza o nome do VM a criar *(DNS01* no cenário), a conta de administrador local e a palavra-passe.
   
    ![Criar VM no portal Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Selecione rede**Network** > **virtual**de **configuração** > opcional, e, em seguida, selecione **TestVNet**. Se o **TestVNet** não estiver disponível, certifique-se de que está a utilizar a localização *dos EUA Centrais* e criou o ambiente de teste descrito no início deste artigo.
   
    ![Criar VM no portal Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. No âmbito **da Rede**, certifique-se de que a sub-rede atualmente selecionada é *FrontEnd*, em seguida, selecione **endereços IP**, sob a sede de atribuição de **endereçoIP** **Static,** e, em seguida, introduza *192.168.1.101* para **ip Address** como visto abaixo.
   
    ![Criar VM no portal Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Selecione **OK** sob **endereços IP,** selecione **OK** sob **rede**, e, em seguida, selecione **OK** sob **config opcional**.
7. Em **'Criar VM',** selecione **Criar**. Repare no azulejo abaixo exibido no seu painel de instrumentos:
   
    ![Criar VM no portal Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações estáticas de endereço ip privado para um VM
Para visualizar as informações estáticas de endereço ip privado para o VM criados com os passos acima, execute os passos abaixo.

1. A partir do portal Azure, selecione **BROWSE TODAS as** > **máquinas virtuais (clássicas)** > **DNS01** > Todos os**endereços IP** de**definições** > e note a atribuição de endereçoIP e endereço IP como visto abaixo.
   
    ![Criar VM no portal Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de um VM

Nos **endereços IP**, selecione **Dynamic** à direita da atribuição de **endereços IP,** selecione **Guardar**, e, em seguida, selecione **Sim**, como mostrado na seguinte imagem:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a um VM existente

1. Nos **endereços IP,** mostrados anteriormente, selecione **Static** à direita da atribuição de **endereçoIP**.
2. Tipo *192.168.1.101* para **endereço IP,** selecione **Guardar**, e depois selecione **Sim**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Definir endereços IP dentro do sistema operativo

Recomenda-se que não atribua estáticamente o IP privado atribuído à máquina virtual Azure dentro do sistema operativo de um VM, a menos que seja necessário. Se definir manualmente o endereço IP privado dentro do sistema operativo, certifique-se de que é o mesmo endereço do endereço IP privado atribuído ao VM Azure, ou pode perder a conectividade com a máquina virtual. Nunca deve atribuir manualmente o endereço IP público atribuído a uma máquina virtual Azure dentro do sistema operativo da máquina virtual.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre endereços [IP públicos reservados.](virtual-networks-reserved-public-ip.md)
* Conheça os endereços [IP (ILPIP) públicos de nível de instância.](virtual-networks-instance-level-public-ip.md)
* Consulte as [APIs DE REPOUSO IP Reservadas](https://msdn.microsoft.com/library/azure/dn722420.aspx).

