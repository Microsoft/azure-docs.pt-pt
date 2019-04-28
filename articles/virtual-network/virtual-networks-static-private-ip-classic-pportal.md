---
title: Configurar endereços IP privados para as VMs (clássico) - portal do Azure | Documentos da Microsoft
description: Saiba como configurar os endereços IP privados para máquinas virtuais (clássico) com o portal do Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: 72d1c4d2ea3adf7d8751adfbb013435f8f2530f0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125751"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configurar endereços IP privados para uma máquina virtual (clássico) com o portal do Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [gerir um endereço IP privado estático no modelo de implementação do Resource Manager](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os passos de exemplo que se seguem esperam um ambiente simples já criado. Se quiser executar os passos à medida que são apresentadas neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar uma VM
Para criar uma VM com o nome *DNS01* no *front-end* sub-rede de uma VNet com o nome *TestVNet* com um IP privado estático da *192.168.1.101*e completo os seguintes passos:

1. Num browser, navegue para https://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Selecione **NEW** > **computação** > **Windows Server 2012 R2 Datacenter**, tenha em atenção que o **selecionar um modelo de implementação** já lista mostra **clássico**e, em seguida, selecione **criar**.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Sob **Create VM**, introduza o nome da VM seja criada (*DNS01* no cenário), a conta de administrador local e a palavra-passe.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Selecione **configuração opcional** > **rede** > **rede Virtual**e, em seguida, selecione **TestVNet** . Se **TestVNet** não estiver disponível, certifique-se de que está a utilizar o *centro dos E.U.A.* localização e tiver criado o ambiente de teste descrito no início deste artigo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Sob **rede**, certifique-se a sub-rede selecionada atualmente está *front-end*, em seguida, selecione **endereços IP**, em **atribuição de endereços IP** Selecione **estático**e, em seguida, introduza *192.168.1.101* para **endereço IP** como mostrado abaixo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Selecione **OK** sob **endereços IP**, selecione **OK** sob **rede**e, em seguida, selecione **OK** em **Config opcional**.
7. Sob **Create VM**, selecione **criar**. Observe o mosaico abaixo apresentado no seu dashboard:
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter estáticas informações de endereço IP privadas para uma VM
Para ver as estáticas informações de endereço IP privadas para a VM criada com os passos acima, execute os passos abaixo.

1. A partir do portal do Azure, selecione **Procurar tudo** > **máquinas virtuais (clássicas)** > **DNS01** > **todos os as definições** > **endereços IP** e repare que a atribuição de endereços IP e o endereço IP, como mostrado abaixo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático a partir de uma VM

Sob **endereços IP**, selecione **dinâmico** à direita da **atribuição de endereços IP**, selecione **guardar**e, em seguida, selecione  **Sim**, conforme mostrado na imagem seguinte:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a uma VM existente

1. Sob **endereços IP**, mostrado anteriormente, selecione **estático** à direita da **atribuição de endereços IP**.
2. Tipo *192.168.1.101* para **endereço IP**, selecione **guardar**e, em seguida, selecione **Sim**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Conjunto de endereços IP no sistema operativo

É recomendável que não atribuir estaticamente IP privado atribuído à máquina virtual do Azure no sistema operativo de uma VM, a menos que necessário. Se definir manualmente o endereço IP privado no sistema operativo, certifique-se de que é o mesmo endereço como o endereço IP privado atribuído à VM do Azure ou pode perder a conectividade para a máquina virtual. Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure no sistema de operativo da máquina virtual.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [reservado de IP público](virtual-networks-reserved-public-ip.md) endereços.
* Saiba mais sobre [IP público (ILPIP) de nível de instância](virtual-networks-instance-level-public-ip.md) endereços.
* Consulte a [reservado IP REST APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx).

