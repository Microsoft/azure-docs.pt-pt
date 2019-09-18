---
title: Configurar endereços IP privados para VMs (clássicas)-portal do Azure | Microsoft Docs
description: Saiba como configurar endereços IP privados para máquinas virtuais (clássicas) usando o portal do Azure.
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
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059113"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configurar endereços IP privados para uma máquina virtual (clássica) usando o portal do Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [gerir um endereço IP privado estático no modelo de implementação do Resource Manager](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de exemplo a seguir esperam um ambiente simples já criado. Se você quiser executar as etapas conforme elas são exibidas neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar uma VM
Para criar uma VM denominada *DNS01* na sub-rede *frontend* de uma VNet denominada *TestVNet* com um IP privado estático de *192.168.1.101*, conclua as seguintes etapas:

1. Num browser, navegue para https://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Selecione **novo** > **computação**Windows Server 2012 R2 Datacenter, observe que a lista Selecionar um modelo de implantação já mostra o clássico e, em seguida, selecione criar. > 
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Em **criar VM**, insira o nome da VM a ser criada (*DNS01* no cenário), a conta de administrador local e a senha.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Selecione **configuração** > opcional rede rede virtual e, em seguida, selecione TestVNet. >  Se **TestVNet** não estiver disponível, verifique se você está usando o local *EUA Central* e criou o ambiente de teste descrito no início deste artigo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Em **rede**, verifique se a sub-rede selecionada no momento *é frontend*, em seguida, selecione **endereços IP**, em **atribuição de endereço IP** , selecione **estático**e, em seguida, insira *192.168.1.101* para **endereço IP** como visto acima.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Selecione **OK** em **endereços IP**, selecione **OK** em **rede**e, em seguida, selecione **OK** em **configuração opcional**.
7. Em **criar VM**, selecione **criar**. Observe o bloco abaixo exibido em seu painel:
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter estáticas informações de endereço IP privadas para uma VM
Para exibir as informações de endereço IP privado estático para a VM criada com as etapas acima, execute as etapas abaixo.

1. Na portal do Azure, selecione **procurar todas as** > **máquinas virtuais (clássicas)**  > **DNS01** > **todas as configurações** > **endereços IP** e observe a atribuição de endereço IP e Endereço IP como mostrado abaixo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático a partir de uma VM

Em **endereços IP**, selecione **dinâmico** à direita da **atribuição de endereço IP**, selecione **salvar**e, em seguida, selecione **Sim**, conforme mostrado na figura a seguir:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a uma VM existente

1. Em **endereços IP**, mostrados anteriormente, selecione **estático** à direita de **atribuição de endereço IP**.
2. Digite *192.168.1.101* para **endereço IP**, selecione **salvar**e, em seguida, selecione **Sim**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Conjunto de endereços IP no sistema operativo

É recomendável que não atribuir estaticamente IP privado atribuído à máquina virtual do Azure no sistema operativo de uma VM, a menos que necessário. Se definir manualmente o endereço IP privado no sistema operativo, certifique-se de que é o mesmo endereço como o endereço IP privado atribuído à VM do Azure ou pode perder a conectividade para a máquina virtual. Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [reservado de IP público](virtual-networks-reserved-public-ip.md) endereços.
* Saiba mais sobre [IP público (ILPIP) de nível de instância](virtual-networks-instance-level-public-ip.md) endereços.
* Consulte a [reservado IP REST APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx).

