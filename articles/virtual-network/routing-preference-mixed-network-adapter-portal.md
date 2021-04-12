---
title: Configure ambas as opções de preferência de encaminhamento para uma máquina virtual - Portal Azure
description: Saiba como ativar ambas as escolhas de preferência de encaminhamento
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101680149"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>Configurar ambas as opções de preferência de encaminhamento para uma máquina virtual

Este artigo mostra-lhe como configurar ambas as opções [de preferência de encaminhamento](./routing-preference-overview.md) (Internet e rede global da Microsoft) para uma máquina virtual (VM). Isto é conseguido usando duas interfaces de rede virtuais, uma interface de rede com um IP público encaminhado através da rede global da Microsoft, e a outra com um IP público encaminhado através de uma rede ISP.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma máquina virtual com um endereço IP público seguindo as instruções descritas na [Criar uma máquina virtual com um endereço IP público estático utilizando o portal Azure](./virtual-network-deploy-static-pip-arm-portal.md) utilizando o portal Azure. A escolha de preferência de encaminhamento padrão para o IP público é através da **rede global da Microsoft.** Assim que tiver uma máquina virtual com um IP público criado, adicione um segundo IP público ao VM que encaminha o tráfego através da rede ISP de trânsito com a sua preferência de encaminhamento configurada como **Internet**.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>Criar um endereço IP público com uma escolha de preferência de encaminhamento Internet
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, *digite o endereço IP público.*
4. Nos resultados da pesquisa, selecione **o endereço IP público.** Em seguida, na página **de endereços IP público,** selecione **Criar**.
5. Nas opções **de preferência de encaminhamento,** selecione **Internet**.

      ![Criar um endereço IP público](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > Os endereços IP públicos são criados com um endereço IPv4 ou IPv6. No entanto, a preferência de encaminhamento apenas suporta o IPV4 atualmente.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>Criar uma interface de rede e associar o IP público

1. Crie uma [interface de rede](./routing-preference-overview.md) utilizando o portal Azure com definições predefinidas. 
1. [Associar o IP público à interface de rede](./associate-public-ip-address-vm.md) criada na secção anterior. Pode levar alguns segundos para aparecer um endereço IP. Ver o endereço IP público atribuído à configuração IP, como mostrado:

    ![Associar o IP Público](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>Anexar interface de rede ao VM

1. [Fixe a interface de rede criada na secção anterior à máquina virtual](./virtual-network-network-interface-vm.md).
2. Agora pode ver duas interfaces de rede virtuais associadas à máquina virtual, uma com um IP público que é encaminhado através da rede global da Microsoft, e a outra encaminhada através de uma rede ISP como mostrado:  ![ Anexar uma interface de rede a um VM](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [IP público com preferência por encaminhamento](routing-preference-overview.md).
- [Configure a preferência de encaminhamento para um VM](tutorial-routing-preference-virtual-machine-portal.md).
- [Configure a preferência de encaminhamento para um endereço IP público utilizando o PowerShell](routing-preference-powershell.md).
- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).