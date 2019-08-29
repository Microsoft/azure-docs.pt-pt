---
title: Abrir portas para uma VM usando o portal do Azure | Microsoft Docs
description: Saiba como abrir uma porta/criar um ponto de extremidade para sua VM do Windows usando o modelo de implantação do Gerenciador de recursos no portal do Azure
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
ms.openlocfilehash: f9edee7a0ff19a536d0ea719ede6d0cd2e9d6ac7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102639"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Como abrir portas para uma máquina virtual com o portal do Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Procure e selecione o grupo de recursos para a VM, escolha **Adicionar**, procure e selecione grupo de **segurança de rede**.

2. Selecione **Criar**.

    A janela **Criar grupo de segurança de rede** é aberta.

    ![Criar um grupo de segurança de rede](./media/nsg-quickstart-portal/create-nsg.png)

2. Insira um nome para seu grupo de segurança de rede. 

3. Selecione ou crie um grupo de recursos e, em seguida, selecione um local.

4. Selecione **criar** para criar o grupo de segurança de rede.

## <a name="create-an-inbound-security-rule"></a>Criar uma regra de segurança de entrada

1. Selecione o novo grupo de segurança de rede. 

2. Selecione **regras de segurança de entrada**e, em seguida, selecione **Adicionar**.

    ![Adicionar regra de entrada](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Selecione **Avançadas**. 

4. Escolha um **serviço** comum no menu suspenso, como **http**. Você também pode selecionar **personalizado** se desejar fornecer uma porta específica a ser usada. 

5. Opcionalmente, altere a **prioridade** ou o **nome**. A prioridade afeta a ordem na qual as regras são aplicadas: quanto menor o valor numérico, mais cedo a regra é aplicada.

6. Selecione **Adicionar** para criar a regra.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associar o grupo de segurança de rede a uma sub-rede

O passo final é associar o grupo de segurança de rede a uma sub-rede ou uma interface de rede específica. Para este exemplo, associaremos o grupo de segurança de rede a uma sub-rede. 

1. Selecione **sub-redes**e, em seguida, selecione **associar**.

    ![Associar um grupo de segurança de rede a uma sub-rede](./media/nsg-quickstart-portal/associate-subnet.png)

2. Selecione a rede virtual e, em seguida, selecione a sub-rede apropriada.

    ![Associando um grupo de segurança de rede com rede virtual](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Todas as VMs que você conectar a essa sub-rede agora estarão acessíveis na porta 80.

## <a name="additional-information"></a>Informações adicionais

Você também pode [executar as etapas neste artigo usando Azure PowerShell](nsg-quickstart-powershell.md).

Os comandos descritos neste artigo permitem que você obtenha rapidamente o tráfego que flui para sua VM. Os grupos de segurança de rede fornecem muitos ótimos recursos e granularidade para controlar o acesso aos seus recursos. Para obter mais informações, consulte [filtrar o tráfego de rede com um grupo de segurança de rede](../../virtual-network/tutorial-filter-network-traffic.md).

Para aplicativos Web altamente disponíveis, considere colocar suas VMs por trás de um balanceador de carga do Azure. O balanceador de carga distribui o tráfego para VMs, com um grupo de segurança de rede que fornece filtragem de tráfego. Para obter mais informações, consulte [balancear carga de máquinas virtuais do Windows no Azure para criar um aplicativo altamente disponível](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você criou um grupo de segurança de rede, criou uma regra de entrada que permite o tráfego HTTP na porta 80 e, em seguida, associou essa regra a uma sub-rede. 

Você pode encontrar informações sobre como criar ambientes mais detalhados nos seguintes artigos:
- [Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
- [Grupos de segurança](../../virtual-network/security-overview.md)