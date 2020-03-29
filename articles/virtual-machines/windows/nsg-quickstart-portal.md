---
title: Abrir portas para um VM usando o portal Azure
description: Saiba como abrir uma porta /criar um ponto final para o seu Windows VM utilizando o modelo de implementação do gestor de recursos no Portal Azure
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 130d3315b5a9a6f175bd3d67ed33a034ab5f8dda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371416"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Como abrir portas a uma máquina virtual com o portal Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Procure e selecione o grupo de recursos para o VM, escolha **Adicionar,** em seguida, procure e selecione o grupo de **segurança da rede**.

2. Selecione **Criar**.

    A janela do grupo de **segurança da rede Create** abre-se.

    ![Criar um grupo de segurança de rede](./media/nsg-quickstart-portal/create-nsg.png)

2. Insira um nome para o seu grupo de segurança de rede. 

3. Selecione ou crie um grupo de recursos e, em seguida, selecione uma localização.

4. Selecione **Criar** para criar o grupo de segurança da rede.

## <a name="create-an-inbound-security-rule"></a>Criar uma regra de segurança de entrada

1. Selecione o seu novo grupo de segurança de rede. 

2. Selecione **as regras de segurança de entrada**e, em seguida, selecione **Adicionar**.

    ![Adicionar regra de entrada](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Selecione **Advanced**. 

4. Escolha um **Serviço** comum a partir do menu suspenso, como **HTTP**. Também pode selecionar **O Costume** se pretender fornecer uma porta específica para usar. 

5. Opcionalmente, altere a **Prioridade** ou **Nome**. A prioridade afeta a ordem em que as regras são aplicadas: quanto menor for o valor numérico, mais cedo a regra é aplicada.

6. Selecione **Adicionar** para criar a regra.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associe o seu grupo de segurança de rede a uma subnet

O passo final é associar o grupo de segurança de rede a uma sub-rede ou uma interface de rede específica. Por este exemplo, associaremos o grupo de segurança da rede a uma sub-rede. 

1. Selecione **Subnets**e, em seguida, selecione **Associado**.

    ![Associar um grupo de segurança de rede com uma subnet](./media/nsg-quickstart-portal/associate-subnet.png)

2. Selecione a rede virtual e, em seguida, selecione a sub-rede apropriada.

    ![Associar um grupo de segurança de rede com networking](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Quaisquer VMs que ligues a essa sub-rede são agora acessíveis na porta 80.

## <a name="additional-information"></a>Informações adicionais

Também pode [executar os passos deste artigo utilizando o Azure PowerShell](nsg-quickstart-powershell.md).

Os comandos descritos neste artigo permitem-lhe obter rapidamente o tráfego fluindo para o seu VM. Os grupos de segurança da rede fornecem muitas grandes funcionalidades e granularidade para controlar o acesso aos seus recursos. Para mais informações, consulte o tráfego da [rede Filter com um grupo de segurança](../../virtual-network/tutorial-filter-network-traffic.md)da rede .

Para aplicações web altamente disponíveis, considere colocar os seus VMs atrás de um equilibrador de carga Azure. O equilibrador de carga distribui tráfego para VMs, com um grupo de segurança de rede que fornece filtragem de tráfego. Para mais informações, consulte [o balance Windows virtual machines em Azure para criar uma aplicação altamente disponível.](tutorial-load-balancer.md)

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um grupo de segurança de rede, criou uma regra de entrada que permite o tráfego HTTP na porta 80, e depois associou essa regra a uma subnet. 

Pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:
- [Visão geral do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md)
- [Grupos de segurança](../../virtual-network/security-overview.md)