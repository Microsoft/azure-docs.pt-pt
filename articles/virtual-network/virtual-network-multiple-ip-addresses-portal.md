---
title: Vários endereços IP para máquinas virtuais do Azure-portal | Microsoft Docs
description: Saiba como atribuir vários endereços IP a uma máquina virtual usando o portal do Azure | Gerenciador de recursos.
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
ms.author: anavin
ms.openlocfilehash: 66cbb843369dee103f102c9c743da544a833ccf1
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646399"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Atribuir vários endereços IP a máquinas virtuais usando o portal do Azure

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Este artigo explica como criar uma VM (máquina virtual) por meio do modelo de implantação Azure Resource Manager usando o portal do Azure. Vários endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre os modelos de implantação do Azure, leia o artigo [entender os modelos de implantação](../resource-manager-deployment-model.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

Se você quiser criar uma VM com vários endereços IP ou um endereço IP privado estático, você deverá criá-lo usando o PowerShell ou o CLI do Azure. Para saber como, clique nas opções do PowerShell ou da CLI na parte superior deste artigo. Você pode criar uma VM com um único endereço IP privado dinâmico e (opcionalmente) um único endereço IP público. Use o portal seguindo as etapas nos artigos [criar uma VM do Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [criar uma VM do Linux](../virtual-machines/linux/quick-create-portal.md) . Depois de criar a VM, você pode alterar o tipo de endereço IP de dinâmico para estático e adicionar outros endereços IP usando o portal seguindo as etapas na seção [adicionar endereços IP a uma VM](#add) deste artigo.

## <a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados a uma interface de rede do Azure concluindo as etapas a seguir. Os exemplos nas seções a seguir pressupõem que você já tem uma VM com as três configurações de IP descritas no [cenário](#scenario), mas isso não é necessário.

### <a name="coreadd"></a>Etapas principais

1. Navegue até o portal do Azure em https://portal.azure.com e entre nele, se necessário.
2. No portal, clique em **mais serviços** > digite *máquinas virtuais* na caixa de filtro e clique em **máquinas virtuais**.
3. No painel **máquinas virtuais** , clique na VM à qual você deseja adicionar endereços IP. Clique em **adaptadores de rede** no painel máquina virtual que aparece e selecione a interface de rede à qual você deseja adicionar os endereços IP. No exemplo mostrado na figura a seguir, a NIC chamada *myNIC* da VM denominada *myVM* está selecionada:

    ![Interface de rede](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. No painel que aparece para a NIC que você selecionou, clique em **configurações de IP**.

Conclua as etapas em uma das seções a seguir, com base no tipo de endereço IP que você deseja adicionar.

### <a name="add-a-private-ip-address"></a>**Adicionar um endereço IP privado**

Conclua as seguintes etapas para adicionar um novo endereço IP privado:

1. Conclua as etapas na seção [etapas principais](#coreadd) deste artigo.
2. Clique em **Adicionar**. No painel **Adicionar configuração de IP** que aparece, crie uma configuração de IP chamada *ipconfig-4* com *10.0.0.7* como um endereço IP privado *estático* e clique em **OK**.

    > [!NOTE]
    > Ao adicionar um endereço IP estático, você deve especificar um endereço válido e não usado na sub-rede à qual a NIC está conectada. Se o endereço selecionado não estiver disponível, o portal exibirá um X para o endereço IP e você deverá selecionar um diferente.

3. Depois de clicar em OK, o painel será fechado e você verá a nova configuração de IP listada. Clique em **OK** para fechar o painel **Adicionar configuração de IP** .
4. Você pode clicar em **Adicionar** para adicionar outras configurações de IP ou fechar todas as folhas abertas para concluir a adição de endereços IP.
5. Adicione os endereços IP privados ao sistema operacional da VM concluindo as etapas na seção [adicionar endereços IP a um sistema operacional da VM](#os-config) deste artigo.

### <a name="add-a-public-ip-address"></a>Adicionar um endereço IP público

Um endereço IP público é adicionado pela Associação de um recurso de endereço IP público a uma nova configuração de IP ou uma configuração de IP existente.

> [!NOTE]
> Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre preços de endereço IP, leia a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) (Limites do artigo).
> 

### <a name="create-public-ip"></a>Criar um recurso de endereço IP público

Um endereço IP público é uma configuração para um recurso de endereço IP público. Se você tiver um recurso de endereço IP público que não esteja atualmente associado a uma configuração de IP que você deseja associar a uma configuração de IP, ignore as etapas a seguir e conclua as etapas em uma das seções a seguir, conforme necessário. Se você não tiver um recurso de endereço IP público disponível, conclua as seguintes etapas para criar um:

1. Navegue até o portal do Azure em https://portal.azure.com e entre nele, se necessário.
3. No portal, clique em **criar um recurso** > **rede** > **endereço IP público**.
4. No painel **criar endereço IP público** que aparece, insira um **nome**, selecione um tipo de **atribuição de endereço IP** , uma **assinatura**, um **grupo de recursos**e um **local**e, em seguida, clique em **criar**, conforme mostrado na figura a seguir:

    ![Criar um recurso de endereço IP público](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Conclua as etapas em uma das seções a seguir para associar o recurso de endereço IP público a uma configuração de IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associar o recurso de endereço IP público a uma nova configuração de IP

1. Conclua as etapas na seção [etapas principais](#coreadd) deste artigo.
2. Clique em **Adicionar**. No painel **Adicionar configuração de IP** que aparece, crie uma configuração de IP chamada *ipconfig-4*. Habilite o **endereço IP público** e selecione um recurso de endereço IP público existente disponível no painel **escolher endereço IP público** que aparece.

    Depois de selecionar o recurso de endereço IP público, clique em **OK** e o painel será fechado. Se você não tiver um endereço IP público existente, poderá criar um, concluindo as etapas na seção [criar um recurso de endereço IP público](#create-public-ip) deste artigo. 

3. Examine a nova configuração de IP. Embora um endereço IP privado não tenha sido explicitamente atribuído, um foi atribuído automaticamente à configuração de IP, pois todas as configurações de IP devem ter um endereço IP privado.
4. Você pode clicar em **Adicionar** para adicionar outras configurações de IP ou fechar todas as folhas abertas para concluir a adição de endereços IP.
5. Adicione o endereço IP privado ao sistema operacional da VM concluindo as etapas para seu sistema operacional na seção [adicionar endereços IP a um sistema operacional da VM](#os-config) deste artigo. Não adicione o endereço IP público ao sistema operacional.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associar o recurso de endereço IP público a uma configuração de IP existente

1. Conclua as etapas na seção [etapas principais](#coreadd) deste artigo.
2. Clique na configuração de IP à qual você deseja adicionar o recurso de endereço IP público.
3. No painel IPConfig que aparece, clique em **endereço IP**.
4. No painel **escolher endereço IP público** que aparece, selecione um endereço IP público.
5. Clique em **salvar** e os painéis são fechados. Se você não tiver um endereço IP público existente, poderá criar um, concluindo as etapas na seção [criar um recurso de endereço IP público](#create-public-ip) deste artigo.
3. Examine a nova configuração de IP.
4. Você pode clicar em **Adicionar** para adicionar outras configurações de IP ou fechar todas as folhas abertas para concluir a adição de endereços IP. Não adicione o endereço IP público ao sistema operacional.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
