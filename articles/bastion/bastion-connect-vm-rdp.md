---
title: Conectar-se a uma VM do Windows usando a bastiões do Azure | Microsoft Docs
description: Neste artigo, saiba como se conectar a uma máquina virtual do Azure que executa o Windows usando a bastiões do Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: dc741007c7de8d8e24f9c0f9e4e0c03306d036a4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498379"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Conectar-se a uma máquina virtual do Windows usando a bastiões do Azure

Este artigo mostra como usar o RDP de forma segura e direta para suas VMs do Windows em uma rede virtual do Azure usando a bastiões do Azure. Pode ligar a uma VM diretamente a partir do portal do Azure. Ao utilizar o Azure Bastion, as VMs não precisam de um cliente, agente ou software adicional. Para obter mais informações sobre a bastiões do Azure, consulte a [visão geral](bastion-overview.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se você configurou um host de bastiões do Azure para a rede virtual na qual a VM reside. Para obter mais informações, consulte [criar um host de bastiões do Azure](bastion-create-host-portal.md). Depois que o serviço de bastiões for provisionado e implantado em sua rede virtual, você poderá usá-lo para se conectar a qualquer VM nessa rede virtual. A bastiões pressupõe que você esteja usando o RDP para se conectar a uma VM do Windows e o SSH para se conectar às suas VMs do Linux. Para obter informações sobre a conexão a uma VM do Linux, consulte [conectar-se a uma VM-Linux](bastion-connect-vm-ssh.md).

Para estabelecer uma conexão, as seguintes funções são necessárias:

* Função de leitor na máquina virtual
* Função de leitor na NIC com IP privado da máquina virtual
* Função de leitor no recurso de bastiões do Azure

## <a name="rdp"></a>Conectar usando RDP

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e clique em **conectar**. A VM deve ser uma máquina virtual do Windows ao usar uma conexão RDP.

   ![Conexão de VM](./media/bastion-connect-vm-rdp/connect.png)
1. Depois de clicar em conectar, uma barra lateral é exibida com três guias – RDP, SSH e bastiões. Se a bastiões tiver sido provisionada para a rede virtual, a guia de bastiões estará ativa por padrão. Se você não provisionar a bastiões para a rede virtual, poderá clicar no link para configurar a bastiões. Para obter instruções de configuração, consulte [Configurar a bastiões](bastion-create-host-portal.md).

   ![Conexão de VM](./media/bastion-connect-vm-rdp/bastion.png)
1. Na guia bastião, o nome de usuário e a senha para sua máquina virtual e clique em **conectar**. A conexão RDP com essa máquina virtual via bastiões será aberta diretamente no portal do Azure (sobre o HTML5) usando a porta 443 e o serviço de bastiões.

   ![Conexão de VM](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Passos seguintes

Leia as [perguntas frequentes de bastiões](bastion-faq.md)