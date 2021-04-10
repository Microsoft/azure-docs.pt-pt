---
title: Abrir portas a um VM utilizando o portal Azure
description: Saiba como abrir uma porta / crie um ponto final para o seu VM utilizando o portal Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 28b3ee98d44e1567d9e25cf9237015396975ab01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550455"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Como abrir portas a uma máquina virtual com o portal Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Procure e selecione o grupo de recursos para o VM, escolha **Adicionar,** em seguida, procure e selecione **grupo de segurança da Rede**.

1. Selecione **Criar**.

    A janela **do grupo de segurança de rede Create** abre.- Abre-se.

    ![Criar um grupo de segurança de rede](./media/nsg-quickstart-portal/create-nsg.png)

1. Insira um nome para o seu grupo de segurança de rede. 

1. Selecione ou crie um grupo de recursos e, em seguida, selecione uma localização.

1. Selecione **Criar** para criar o grupo de segurança de rede.

## <a name="create-an-inbound-security-rule"></a>Criar uma regra de segurança de entrada

1. Selecione o seu novo grupo de segurança de rede. 

1. Selecione **as regras de segurança de entrada** no menu esquerdo e, em seguida, selecione **Adicionar**.

    ![Alternar para a página avançada](./media/nsg-quickstart-portal/advanced.png)

1. Na página **De adicionar uma regra de segurança de entrada,** alternar para **Advanced** from **Basic** no topo da página. 

1. Escolha um **serviço** comum no menu suspenso, como **HTTP**. Também pode selecionar **Custom** se quiser fornecer uma porta específica para usar. 

1. Opcionalmente, altere a **Prioridade** ou **Nome**. A prioridade afeta a ordem em que as regras são aplicadas: quanto menor o valor numérico, mais cedo a regra é aplicada.

1. **Selecione Adicionar** para criar a regra.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associe o seu grupo de segurança de rede a uma sub-rede

O passo final é associar o grupo de segurança de rede a uma sub-rede ou uma interface de rede específica. Para este exemplo, associaremos o grupo de segurança da rede a uma sub-rede. 

1. Selecione **sub-redes** do menu esquerdo e, em seguida, **selecione Associate**.

1. Selecione a rede virtual e, em seguida, selecione a sub-rede apropriada.

    ![Associar um grupo de segurança de rede com networking virtual](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. Quando tiver terminado, selecione **OK**.

## <a name="additional-information"></a>Informações adicionais

Também pode [executar os passos deste artigo utilizando a Azure PowerShell](nsg-quickstart-powershell.md).

Os comandos descritos neste artigo permitem-lhe fazer fluir rapidamente o tráfego para o seu VM. Os grupos de segurança da rede fornecem muitas funcionalidades e granularidade para controlar o acesso aos seus recursos. Para obter mais informações, consulte [o tráfego da rede Filter com um grupo de segurança de rede.](../../virtual-network/tutorial-filter-network-traffic.md)

Para aplicações web altamente disponíveis, considere colocar os seus VMs atrás de um equilibrador de carga Azure. O equilibrador de carga distribui o tráfego para VMs, com um grupo de segurança de rede que fornece filtragem de tráfego. Para obter mais informações, consulte [load balance Windows virtual machines in Azure para criar uma aplicação altamente disponível.](tutorial-load-balancer.md)

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um grupo de segurança de rede, criou uma regra de entrada que permite o tráfego HTTP na porta 80, e depois associou essa regra a uma sub-rede. 

Pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:
- [Visão geral do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md)
- [Grupos de segurança](../../virtual-network/network-security-groups-overview.md)
