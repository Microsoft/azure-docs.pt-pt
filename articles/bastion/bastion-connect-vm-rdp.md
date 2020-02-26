---
title: Ligue-se a um VM do Windows usando o Bastião Azure
description: Neste artigo, aprenda a ligar-se a uma Máquina Virtual Azure que executa o Windows utilizando o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597354"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Ligue-se a uma máquina virtual do Windows usando o Bastião Azure

Utilizando o Azure Bastion, pode ligar-se de forma segura e perfeita às suas máquinas virtuais através da SSL diretamente no portal Azure. Quando utiliza o Azure Bastion, os seus VMs não requerem um cliente, agente ou software adicional. Este artigo mostra-lhe como se conectar aos seus VMs do Windows. Para obter informações sobre a ligação a um Linux VM, consulte [Connect a um VM utilizando o Azure Bastion - Linux](bastion-connect-vm-ssh.md).

A Azure Bastion proporciona uma conectividade segura a todos os VMs na rede virtual em que é aprovisionado. A utilização do Azure Bastion protege as suas máquinas virtuais de expor portas RDP/SSH ao mundo exterior, ao mesmo tempo que proporciona acesso seguro utilizando RDP/SSH. Para obter mais informações, consulte [Descrição Geral](bastion-overview.md).

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que criou um anfitrião do Azure Bastion para a rede virtual em que o VM está localizado. Assim que o serviço Bastion for aprovisionado e implantado na sua rede virtual, pode usá-lo para se ligar a qualquer VM da rede virtual. Para criar um anfitrião do Azure Bastion, consulte [Create a Azure Bastion host](bastion-create-host-portal.md).

### <a name="required-roles"></a>Funções necessárias

Para efazer uma ligação, são necessárias as seguintes funções:

* Papel do leitor na máquina virtual
* Papel do leitor no NIC com IP privado da máquina virtual
* Papel do leitor no recurso Azure Bastion

### <a name="ports"></a>Portas

Para ligar ao Windows VM, deve ter as seguintes portas abertas no seu Windows VM:

* Portas de entrada: PDR (3389)

## <a name="rdp"></a>Ligar

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue para a máquina virtual a que pretende ligar, em seguida, clique em **Ligar** e selecione **Bastion** a partir do dropdown.

   ![Ligação VM](./media/bastion-connect-vm-rdp/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três separadores – RDP, SSH e Bastion. Se a Bastion foi aprovisionada para a rede virtual, o separador Bastion está ativo por defeito. Se não disponibilizou a Bastion para a rede virtual, pode clicar no link para configurar o Bastion. Para instruções de configuração, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![Guia de bastião](./media/bastion-connect-vm-rdp/bastion.png)
1. No separador Bastion, insera o nome de utilizador e a palavra-passe para a sua máquina virtual e, em seguida, clique em **Connect**. A ligação RDP a esta máquina virtual via Bastion abrirá diretamente no portal Azure (mais HTML5) utilizando a porta 443 e o serviço Bastião.

   ![Ligação RDP](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Passos seguintes

Leia o [Bastião FAQ](bastion-faq.md)
