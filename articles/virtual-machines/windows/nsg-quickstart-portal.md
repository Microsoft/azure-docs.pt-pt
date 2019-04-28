---
title: Abrir portas para uma VM com o portal do Azure | Documentos da Microsoft
description: Saiba como abrir uma porta / criar um ponto final para a VM do Windows com o modelo de implementação do resource manager no Portal do Azure
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: cac17403425f53593d4f48692b4216a92c8624e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481773"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Como abrir portas para uma máquina virtual com o portal do Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Procure e selecione o grupo de recursos para a VM, escolha **Add**, em seguida, procure e selecione **grupo de segurança de rede**.

2. Selecione **Criar**.

    O **criar grupo de segurança de rede** é aberta a janela.

    ![Criar um grupo de segurança de rede](./media/nsg-quickstart-portal/create-nsg.png)

2. Introduza um nome para o seu grupo de segurança de rede. 

3. Selecione ou crie um grupo de recursos e, em seguida, selecione uma localização.

4. Selecione **criar** para criar o grupo de segurança de rede.

## <a name="create-an-inbound-security-rule"></a>Criar uma regra de segurança de entrada

1. Selecione o seu novo grupo de segurança de rede. 

2. Selecione **regras de segurança de entrada**, em seguida, selecione **Add**.

    ![Adicionar regra de entrada](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Selecione **Avançadas**. 

4. Escolha uma comum **serviço** no menu pendente, tal como **HTTP**. Também pode selecionar **personalizado** se quiser fornecer uma porta específica a utilizar. 

5. Opcionalmente, altere a **prioridade** ou **nome**. A prioridade afeta a ordem pela qual as regras são aplicadas: menor o valor numérico, quanto mais cedo a regra é aplicada.

6. Selecione **adicionar** para criar a regra.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associar o seu grupo de segurança de rede com uma sub-rede

O passo final é associar o grupo de segurança de rede a uma sub-rede ou uma interface de rede específica. Neste exemplo, podemos irá associar o grupo de segurança de rede com uma sub-rede. 

1. Selecione **sub-redes**, em seguida, selecione **associar**.

    ![Associar um grupo de segurança de rede com uma sub-rede](./media/nsg-quickstart-portal/associate-subnet.png)

2. Selecione a rede virtual e, em seguida, selecione a sub-rede apropriada.

    ![Associar um grupo de segurança de rede com a rede virtual](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Todas as VMs que se liga a essa sub-rede agora se encontram acessíveis na porta 80.

## <a name="additional-information"></a>Informações adicionais

Também pode [efetue os passos neste artigo com o Azure PowerShell](nsg-quickstart-powershell.md).

Os comandos descritos neste artigo permitem-lhe obter rapidamente o tráfego que flui à sua VM. Grupos de segurança de rede fornecem muitos excelentes recursos e granularidade para controlar o acesso aos seus recursos. Para obter mais informações, consulte [filtrar o tráfego de rede com um grupo de segurança de rede](../../virtual-network/tutorial-filter-network-traffic.md).

Para aplicações web de elevada disponibilidade, considere colocar as suas VMs por trás de um balanceador de carga do Azure. O Balanceador de carga distribui o tráfego para VMs, com um grupo de segurança de rede que fornece a filtragem de tráfego. Para obter mais informações, consulte [balanceamento de carga de máquinas virtuais do Windows no Azure para criar uma aplicação de elevada disponibilidade](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, criou um grupo de segurança de rede, criado uma regra de entrada que permita o tráfego HTTP na porta 80 e, em seguida, associadas essa regra com uma sub-rede. 

Pode encontrar informações sobre como criar ambientes mais detalhadas nos seguintes artigos:
- [Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
- [Grupos de segurança](../../virtual-network/security-overview.md)