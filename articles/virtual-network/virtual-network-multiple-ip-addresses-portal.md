---
title: Vários endereços IP para máquinas virtuais do Azure - Portal | Documentos da Microsoft
description: Saiba como atribuir vários endereços IP a uma máquina virtual utilizando o portal do Azure | Gestor de recursos.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
ms.openlocfilehash: 0f36ab9ab7703dd9677b6c2989eddb91593af79f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105112"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Atribuir vários endereços IP para máquinas virtuais no portal do Azure

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Este artigo explica como criar uma máquina virtual (VM) por meio do modelo de implementação Azure Resource Manager com o portal do Azure. Não não possível atribuir vários endereços IP para os recursos criados por meio do modelo de implementação clássica. Para saber mais sobre os modelos de implementação do Azure, leia os [compreender os modelos de implementação](../resource-manager-deployment-model.md) artigo.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

Se quiser criar uma VM com vários endereços IP ou um endereço IP privado estático, tem de criá-lo com o PowerShell ou a CLI do Azure. Para saber como, clique nas opções do PowerShell ou CLI na parte superior deste artigo. Pode criar uma VM com um único endereço IP privado dinâmico e (opcionalmente) um único endereço IP público. Utilizar o portal ao seguir os passos a [criar uma VM do Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [criar uma VM do Linux](../virtual-machines/linux/quick-create-portal.md) artigos. Depois de criar a VM, pode alterar o tipo de endereço IP de dinâmico para estático e adicionar os endereços IP adicionais com o portal ao seguir os passos no [endereços IP de adicionar a uma VM](#add) seção deste artigo.

## <a name="add"></a>Adicionar endereços IP a uma VM

Pode adicionar endereços IP públicos e privados a uma interface de rede do Azure, concluindo os passos que se seguem. Os exemplos nas secções seguintes partem do princípio de que já tem uma VM com as três configurações de IP descritas a [cenário](#Scenario), mas não seja necessário.

### <a name="coreadd"></a>Passos de núcleo

1. Navegue até ao portal do Azure em https://portal.azure.com e inicie sessão na mesma, se necessário.
2. No portal, clique em **mais serviços** > tipo *máquinas virtuais* na caixa de filtro e, em seguida, clique **máquinas virtuais**.
3. Na **máquinas virtuais** painel, clique a VM que pretende adicionar IP de endereços. Clique em **interfaces de rede** na máquina virtual painel que aparece e, em seguida, selecione a interface de rede que pretende adicionar o IP endereços. No exemplo mostrado na imagem seguinte, a NIC com o nome *myNIC* da VM denominada *myVM* está selecionada:

    ![Interface de rede](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. No painel que aparece para o NIC que selecionou, clique em **configurações de IP**.

Conclua os passos de uma das seções a seguir, com base no tipo de endereço IP que pretende adicionar.

### <a name="add-a-private-ip-address"></a>**Adicionar um endereço IP privado**

Conclua os seguintes passos para adicionar um novo endereço IP privado:

1. Conclua os passos a [principais passos](#coreadd) seção deste artigo.
2. Clique em **Adicionar**. Na **configuração de IP adicionar** painel apresentado, crie uma configuração de IP com o nome *IPConfig 4* com *10.0.0.7* como um *estático* IP privado de endereços, em seguida, clique em **OK**.

    > [!NOTE]
    > Ao adicionar um endereço IP estático, tem de especificar um endereço não utilizado, válido na sub-rede que a NIC está ligada. Se o endereço que selecionou não estiver disponível, o portal apresenta um X para o endereço IP e tem de selecionar um diferente.

3. Assim que clicar em OK, fecha o painel e ver a nova configuração de IP listada. Clique em **OK** para fechar a **configuração de IP adicionar** painel.
4. Pode clicar em **adicionar** adicionar configurações de IP adicionais ou fechar painéis abertos para acabar de adicionar endereços IP.
5. Adicionar os endereços IP privados para o sistema operacional VM, concluindo os passos a [endereços IP de adicionar a um sistema de operativo VM](#os-config) seção deste artigo.

### <a name="add-a-public-ip-address"></a>Adicionar um endereço IP público

Um endereço IP público é adicionado ao associar um recurso de endereço IP público para uma nova configuração de IP ou uma configuração de IP existente.

> [!NOTE]
> Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereços IP, leia os [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Existe um limite ao número de endereços IP públicos, que pode ser utilizado numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).
> 

### <a name="create-public-ip"></a>Criar um recurso de endereço IP público

Um endereço IP público é uma definição para um recurso de endereço IP público. Se tiver um recurso de endereço IP público que não está atualmente associado a uma configuração de IP que pretende associar a uma configuração de IP, ignore os passos seguintes e conclua os passos de uma das seções a seguir, que forem necessárias. Se não tiver um recurso de endereço IP público disponível, conclua os seguintes passos para criar um:

1. Navegue até ao portal do Azure em https://portal.azure.com e inicie sessão na mesma, se necessário.
3. No portal, clique em **criar um recurso** > **rede** > **endereço IP público**.
4. Na **Criar endereço IP público** painel apresentado, introduza um **nome**, selecione um **atribuição de endereços IP** tipo, um **subscrição**, um **Grupo de recursos**e um **localização**, em seguida, clique em **criar**, conforme mostrado na imagem seguinte:

    ![Criar um recurso de endereço IP público](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Conclua os passos de uma das seções a seguir para associar o recurso de endereço IP público a uma configuração de IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associar o recurso de endereço IP público para uma nova configuração de IP

1. Conclua os passos a [principais passos](#coreadd) seção deste artigo.
2. Clique em **Adicionar**. Na **configuração do IP de adicionar** painel apresentado, crie uma configuração de IP com o nome *IPConfig 4*. Ativar a **endereço IP público** e selecione um existente, disponível público recurso de endereço IP do **escolher endereço IP público** painel que aparece.

    Depois de selecionar o recurso de endereço IP público, clique em **OK** e fecha o painel. Se não tiver um endereço IP público existente, pode criar uma ao concluir os passos a [criar um recurso de endereço IP público](#create-public-ip) seção deste artigo. 

3. Reveja a nova configuração de IP. Embora explicitamente não foi atribuído um endereço IP privado, um automaticamente foi atribuído à configuração de IP, porque todas as configurações de IP tem de ter um endereço IP privado.
4. Pode clicar em **adicionar** adicionar configurações de IP adicionais ou fechar painéis abertos para acabar de adicionar endereços IP.
5. Adicionar o endereço IP privado para o sistema operacional VM, concluindo os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) seção deste artigo. Não adicione o endereço IP público para o sistema operativo.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associar o recurso de endereço IP público a uma configuração de IP existente

1. Conclua os passos a [principais passos](#coreadd) seção deste artigo.
2. Clique em que pretende adicionar o recurso de endereço IP público para a configuração de IP.
3. No painel de IPConfig que aparece, clique em **endereço IP**.
4. Na **escolher endereço IP público** painel apresentado, selecione um endereço IP público.
5. Clique em **guardar** e fechar os painéis. Se não tiver um endereço IP público existente, pode criar uma ao concluir os passos a [criar um recurso de endereço IP público](#create-public-ip) seção deste artigo.
3. Reveja a nova configuração de IP.
4. Pode clicar em **adicionar** adicionar configurações de IP adicionais ou fechar painéis abertos para acabar de adicionar endereços IP. Não adicione o endereço IP público para o sistema operativo.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
