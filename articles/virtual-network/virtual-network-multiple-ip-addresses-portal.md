---
title: Vários endereços IP para máquinas virtuais Azure - Portal | Microsoft Docs
description: Saiba como atribuir vários endereços IP a uma máquina virtual utilizando o portal Azure | Gestor de Recursos.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: a0dab64aac8bc5fa68a53fad6cd8e6f6bb4bac03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217146"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Atribuir vários endereços IP a máquinas virtuais utilizando o portal Azure

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Este artigo explica como criar uma máquina virtual (VM) através do modelo de implementação do Azure Resource Manager utilizando o portal Azure. Vários endereços IP não podem ser atribuídos a recursos criados através do modelo de implementação clássico. Para saber mais sobre os modelos de implementação do Azure, leia o artigo [de modelos de implementação Understand.](../azure-resource-manager/management/deployment-models.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP

Se pretender criar um VM com vários endereços IP ou um endereço IP estático privado, deve criá-lo utilizando o PowerShell ou o Azure CLI. Para saber como, clique nas opções PowerShell ou CLI no topo deste artigo. Pode criar um VM com um único endereço IP privado dinâmico e (opcionalmente) um único endereço IP público. Utilize o portal seguindo os passos no [Windows VM](../virtual-machines/windows/quick-create-portal.md) ou [crie artigos Linux VM.](../virtual-machines/linux/quick-create-portal.md) Depois de criar o VM, pode alterar o tipo de endereço IP de dinâmico para estático e adicionar endereços IP adicionais utilizando o portal seguindo os passos nos [endereços Add IP para uma](#add) secção VM deste artigo.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Adicionar endereços IP a um VM

Pode adicionar endereços IP privados e públicos a uma interface de rede Azure, completando os passos que se seguem. Os exemplos nas secções seguintes pressupõem que já tem um VM com as três configurações IP descritas no [cenário](#scenario)– mas não é necessário.

### <a name="core-steps"></a><a name="coreadd"></a>Passos principais

1. Procure no portal Azure https://portal.azure.com e assine, se necessário.
2. No portal, clique em **Mais serviços** > tipo *máquinas virtuais* na caixa de filtro e, em seguida, clique em **máquinas Virtuais**.
3. No painel **de máquinas Virtuais,** clique no VM a que pretende adicionar endereços IP. Navegue para **o separador de rede.** Clique na **interface de rede** na página. Como mostra a imagem abaixo: 


    ![Adicionar um endereço IP público a uma VM](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. No painel **de interface da Rede,** clique nas **configurações IP**.

5. No painel que aparece para o NIC selecionado, clique nas **configurações IP**. Clique em **Adicionar,** complete os passos numa das secções que se seguem, com base no tipo de endereço IP que pretende adicionar e, em seguida, clique em **OK**. 

### <a name="add-a-private-ip-address"></a>Adicionar um endereço IP privado

Complete os seguintes passos para adicionar um novo endereço IP privado:

1. Preencha os passos na secção de [passos Core](#coreadd) deste artigo e certifique-se de que está na secção de **configurações IP** da Interface de Rede VM.  Reveja a sub-rede apresentada como padrão (por exemplo, 10.0.0.0/24).
2. Clique em **Adicionar**. No painel de **configuração IP** que aparece, crie uma configuração IP chamada *IPConfig-4* com um novo endereço IP *estático* privado, escolhendo um novo número para o octeto final e, em seguida, clique em **OK**.  (Para a sub-rede 10.0.0.0/24, um IP de exemplo seria *10.0.0.7*.)

    > [!NOTE]
    > Ao adicionar um endereço IP estático, deve especificar um endereço válido não ustado na sub-rede a que o NIC está ligado. Se o endereço selecionado não estiver disponível, o portal apresenta um X para o endereço IP e tem de selecionar outro.

3. Uma vez que clica OK, o painel fecha-se e vê a nova configuração IP listada. Clique **em OK** para fechar o painel de **configuração DE ADICIONAR IP.**
4. Pode clicar **em Adicionar** para adicionar configurações IP adicionais ou fechar todas as lâminas abertas para terminar a adição de endereços IP.
5. Adicione os endereços IP privados ao sistema operativo VM, preenchendo os passos nos [endereços Add IP a uma](#os-config) secção do sistema operativo VM deste artigo.

### <a name="add-a-public-ip-address"></a>Adicionar um endereço IP público

Um endereço IP público é adicionado associando um recurso de endereço IP público a uma nova configuração IP ou a uma configuração IP existente.

> [!NOTE]
> Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre o preço do endereço IP, leia a página de preços do [endereço IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Existe um limite para o número de endereços IP públicos que podem ser usados numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) (Limites do artigo).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Criar um recurso de endereço IP público

Um endereço IP público é uma definição para um recurso de endereço IP público. Se tiver um recurso de endereço IP público que não esteja atualmente associado a uma configuração IP que pretende associar a uma configuração IP, salte os passos seguintes e complete os passos numa das secções que se seguem, conforme necessário. Se não tiver um recurso de endereço IP público disponível, complete os seguintes passos para criar um:

1. Procure no portal Azure https://portal.azure.com e assine, se necessário.
3. No portal, clique em **Criar um** endereço IP  >  público de rede **de**  >  recursos.
4. No painel **de endereços IP público** que aparece, introduza um **Nome**, selecione um tipo de atribuição de **endereço IP,** uma **Subscrição**, um **grupo de Recursos** e uma **Localização**, clique em **Criar**, como mostrado na seguinte imagem:

    ![Criar um recurso de endereço IP público](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Preencha os passos numa das secções que se seguem para associar o recurso de endereço IP público a uma configuração IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associar o recurso de endereço IP público a uma nova configuração IP

1. Complete os passos na secção de [passos do Núcleo](#coreadd) deste artigo.
2. Clique em **Adicionar**. No painel de **configuração IP** que aparece, crie uma configuração IP chamada *IPConfig-4*. Ative o **endereço IP público** e selecione um recurso de endereço IP público existente e disponível a partir do painel de **endereços IP público seletivo** que aparece.

    Uma vez selecionado o recurso de endereço IP público, clique em **OK** e o painel fecha. Se não tiver um endereço IP público existente, pode criar um, preenchendo os passos na secção de recursos ip [públicos](#create-public-ip) deste artigo. 

3. Reveja a nova configuração IP. Apesar de um endereço IP privado não ter sido explicitamente atribuído, um foi automaticamente atribuído à configuração IP, porque todas as configurações IP devem ter um endereço IP privado.
4. Pode clicar **em Adicionar** para adicionar configurações IP adicionais ou fechar todas as lâminas abertas para terminar a adição de endereços IP.
5. Adicione o endereço IP privado ao sistema operativo VM, preenchendo as etapas do seu sistema operativo nos [endereços Add IP a uma](#os-config) secção do sistema operativo VM deste artigo. Não adicione o endereço IP público ao sistema operativo.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associar o recurso de endereço IP público a uma configuração IP existente

1. Complete os passos na secção de [passos do Núcleo](#coreadd) deste artigo.
2. Clique na configuração IP a que pretende adicionar o recurso de endereço IP público.
3. No painel IPConfig que aparece, clique no **endereço IP**.
4. No painel **de endereços IP público** escolha que aparece, selecione um endereço IP público.
5. Clique **em Guardar** e fechar as vidraças. Se não tiver um endereço IP público existente, pode criar um, preenchendo os passos na secção de recursos ip [públicos](#create-public-ip) deste artigo.
3. Reveja a nova configuração IP.
4. Pode clicar **em Adicionar** para adicionar configurações IP adicionais ou fechar todas as lâminas abertas para terminar a adição de endereços IP. Não adicione o endereço IP público ao sistema operativo.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]