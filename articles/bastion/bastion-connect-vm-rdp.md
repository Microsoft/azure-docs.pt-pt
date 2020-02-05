---
title: Ligue-se a um Windows VM utilizando o Bastião Azure  Microsoft Docs
description: Neste artigo, aprenda a ligar-se a uma Máquina Virtual Azure que executa o Windows utilizando o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7dad6a517341f83f693e1e7e1f7d27e899e00f7e
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990492"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Ligue-se a uma máquina virtual do Windows usando o Bastião Azure

Este artigo mostra-lhe como de forma segura e perfeita rdp para os seus VMs Windows numa rede virtual Azure usando o Azure Bastion. Pode ligar a uma VM diretamente a partir do portal do Azure. Ao utilizar o Azure Bastion, as VMs não precisam de um cliente, agente ou software adicional. Para mais informações sobre o Bastião Azure, consulte a [visão geral.](bastion-overview.md)

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que criou um anfitrião do Azure Bastion para a rede virtual em que o VM reside. Para mais informações, consulte [Create a Azure Bastion host](bastion-create-host-portal.md). Assim que o serviço Bastion for aprovisionado e implantado na sua rede virtual, pode usá-lo para se ligar a qualquer VM nesta rede virtual.

A Bastion assume que está a usar RDP para ligar a um VM do Windows e SSH para se ligar aos seus VMs Linux. Para obter informações sobre a ligação a um VM Linux, consulte [Connect to a VM - Linux](bastion-connect-vm-ssh.md).

### <a name="required-roles"></a>Funções necessárias
Para estabelecer uma conexão, as seguintes funções são necessárias:

* Função de leitor na máquina virtual
* Função de leitor na NIC com IP privado da máquina virtual
* Função de leitor no recurso de bastiões do Azure

### <a name="ports"></a>Portas

Para se ligar ao Windows VM via RDP, deve ter as seguintes portas abertas no seu Windows VM:

* Portas de entrada: PDR (3389)

## <a name="rdp"></a>Conecte-se usando RDP

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue na máquina virtual a que pretende ligar e, em seguida, clique em **Connect**. O VM deve ser uma máquina virtual do Windows quando utilizar uma ligação RDP.

   ![Ligação VM](./media/bastion-connect-vm-rdp/connect.png)
1. Depois de clicar em Connect, aparece uma barra lateral que tem três separadores – RDP, SSH e Bastion. Se a Bastion foi aprovisionada para a rede virtual, o separador Bastion está ativo por defeito. Se não disponibilizou a Bastion para a rede virtual, pode clicar no link para configurar o Bastion. Para instruções de configuração, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![Ligação VM](./media/bastion-connect-vm-rdp/bastion.png)
1. No separador Bastion, o nome de utilizador e a palavra-passe para a sua máquina virtual, em seguida, clique em **Connect**. A ligação RDP a esta máquina virtual via Bastion abrirá diretamente no portal Azure (mais HTML5) utilizando a porta 443 e o serviço Bastião.

   ![Ligação VM](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Passos seguintes

Leia o [Bastião FAQ](bastion-faq.md)
