---
title: Múltiplos endereços IP para máquinas virtuais Azure - Portal / Microsoft Docs
description: Saiba como atribuir vários endereços IP a uma máquina virtual utilizando o portal Azure [ Gestor de Recursos.
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
ms.openlocfilehash: a5bb9bb9c584aef8ac79b3c275d01d3c498da843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060617"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Atribuir vários endereços IP a máquinas virtuais utilizando o portal Azure

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Este artigo explica como criar uma máquina virtual (VM) através do modelo de implementação do Gestor de Recursos Azure utilizando o portal Azure. Vários endereços IP não podem ser atribuídos aos recursos criados através do modelo de implementação clássico. Para saber mais sobre os modelos de implementação do Azure, leia o artigo dos [modelos de implementação Da Understand.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP

Se pretender criar um VM com vários endereços IP, ou um endereço IP privado estático, deve criá-lo utilizando o PowerShell ou o Azure CLI. Para saber como, clique nas opções PowerShell ou CLI no topo deste artigo. Pode criar um VM com um único endereço IP privado dinâmico e (opcionalmente) um único endereço IP público. Utilize o portal seguindo os passos no [Create a Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Create a Linux VM.](../virtual-machines/linux/quick-create-portal.md) Depois de criar o VM, pode alterar o tipo de endereço IP de dinâmico para estático e adicionar endereços IP adicionais utilizando o portal seguindo os passos nos [endereços ADD IP para uma](#add) secção VM deste artigo.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Adicione endereços IP a um VM

Pode adicionar endereços IP privados e públicos a uma interface de rede Azure, completando os passos que se seguem. Os exemplos nas seguintes secções assumem que já tem um VM com as três configurações IP descritas no [cenário](#scenario), mas não é necessário.

### <a name="core-steps"></a><a name="coreadd"></a>Passos de núcleo

1. Navegue até ao portal https://portal.azure.com Azure e assine-o, se necessário.
2. No portal, clique em **Mais serviços** > digitar *máquinas virtuais* na caixa de filtro e, em seguida, clicar em **máquinas Virtuais**.
3. No painel de **máquinas Virtuais,** clique no VM a que pretende adicionar endereços IP. Navegue para o separador **de rede.** Clique na **interface da rede** na página. Como mostra a imagem abaixo: 


    ![Adicionar um endereço IP público a uma VM](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. No painel de interface da **Rede,** clique nas **configurações IP**.

5. No painel que aparece para o NIC selecionado, clique em **configurações IP**. Clique em **Adicionar,** complete os passos numa das secções que se seguem, com base no tipo de endereço IP que pretende adicionar e, em seguida, clique em **OK**. 

### <a name="add-a-private-ip-address"></a>**Adicione um endereço IP privado**

Complete os seguintes passos para adicionar um novo endereço IP privado:

1. Complete os passos na secção de [passos core](#coreadd) deste artigo.
2. Clique em **Adicionar**. No painel de **configuração Add IP** que aparece, crie uma configuração IP chamada *IPConfig-4* com *10.0.0.7* como um endereço IP privado *estático* **e,** em seguida, clique OK .

    > [!NOTE]
    > Ao adicionar um endereço IP estático, deve especificar um endereço não utilizado e válido na sub-rede a que o NIC está ligado. Se o endereço que selecionar não estiver disponível, o portal apresenta um X para o endereço IP e deve selecionar um diferente.

3. Uma vez clicado OK, o painel fecha e vê a nova configuração IP listada. Clique **em OK** para fechar o painel de **configuração Add IP.**
4. Pode clicar em **Adicionar** para adicionar configurações IP adicionais ou fechar todas as lâminas abertas para terminar a adição de endereços IP.
5. Adicione os endereços IP privados ao sistema operativo VM, preenchendo as etapas nos [endereços ADD IP a uma](#os-config) secção do sistema operativo VM deste artigo.

### <a name="add-a-public-ip-address"></a>Adicione um endereço IP público

Um endereço IP público é adicionado associando um recurso de endereço IP público a uma nova configuração IP ou uma configuração IP existente.

> [!NOTE]
> Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços do endereço IP, leia a página de preços do [endereço IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Existe um limite para o número de endereços IP públicos que podem ser usados numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) (Limites do artigo).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Criar um recurso público de endereço IP

Um endereço IP público é um dos cenários para um recurso público de endereço IP. Se tiver um recurso de endereço IP público que não esteja atualmente associado a uma configuração IP que pretende associar a uma configuração IP, ignore os seguintes passos e complete os passos numa das secções que se seguem, conforme necessita. Se não tiver um recurso de endereço IP público disponível, complete os seguintes passos para criar um:

1. Navegue até ao portal https://portal.azure.com Azure e assine-o, se necessário.
3. No portal, clique em **Criar um** > endereço**IP público**em**rede** > de recursos .
4. No painel de **endereços IP público criar** que aparece, introduza um **Nome**, selecione um tipo de atribuição de **endereçoIP,** uma **Subscrição,** um **grupo de Recursos,** e uma **Localização,** em seguida, clique em **Criar**, como mostrado na seguinte imagem:

    ![Criar um recurso público de endereço IP](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Complete os passos numa das secções que se seguem para associar o recurso de endereço IP público a uma configuração IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associar o recurso de endereço IP público a uma nova configuração IP

1. Complete os passos na secção de [passos core](#coreadd) deste artigo.
2. Clique em **Adicionar**. No painel de **configuração Add IP** que aparece, crie uma configuração IP chamada *IPConfig-4*. Ative o **endereço IP público** e selecione um recurso ip público existente e disponível do painel de endereços IP público seletivo que aparece. **Choose public IP address**

    Depois de selecionar o recurso público de endereço IP, clique em **OK** e o painel fecha. Se não tiver um endereço IP público existente, pode criar um, preenchendo os passos na secção de recursos de [endereçoIP público](#create-public-ip) deste artigo. 

3. Reveja a nova configuração IP. Mesmo que um endereço IP privado não tenha sido explicitamente atribuído, um foi automaticamente atribuído à configuração IP, porque todas as configurações IP devem ter um endereço IP privado.
4. Pode clicar em **Adicionar** para adicionar configurações IP adicionais ou fechar todas as lâminas abertas para terminar a adição de endereços IP.
5. Adicione o endereço IP privado ao sistema operativo VM preenchendo as etapas do seu sistema operativo nos [endereços IP Add a uma](#os-config) secção do sistema operativo VM deste artigo. Não adicione o endereço IP público ao sistema operativo.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associar o recurso de endereço IP público a uma configuração IP existente

1. Complete os passos na secção de [passos core](#coreadd) deste artigo.
2. Clique na configuração IP a que pretende adicionar o recurso de endereço IP público.
3. No painel IPConfig que aparece, clique no **endereço IP**.
4. No painel de **endereços IP públicos** que aparece, selecione um endereço IP público.
5. Clique em **Guardar** e as vidraças fecham. Se não tiver um endereço IP público existente, pode criar um, preenchendo os passos na secção de recursos de [endereçoIP público](#create-public-ip) deste artigo.
3. Reveja a nova configuração IP.
4. Pode clicar em **Adicionar** para adicionar configurações IP adicionais ou fechar todas as lâminas abertas para terminar a adição de endereços IP. Não adicione o endereço IP público ao sistema operativo.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
