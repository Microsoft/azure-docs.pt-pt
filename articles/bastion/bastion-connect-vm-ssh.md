---
title: Ligue-se a um Linux VM usando o Bastião Azure
description: Neste artigo, aprenda a ligar-se à Máquina Virtual Linux utilizando o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596832"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Ligue usando o SSH a uma máquina virtual Linux usando o Bastião Azure

Este artigo mostra-lhe como obter sSH de forma segura e perfeita para os seus VMs Linux numa rede virtual Azure. Pode ligar-se a um VM diretamente do portal Azure. Ao utilizar o Azure Bastion, os VMs não requerem um cliente, agente ou software adicional. Para mais informações sobre o Bastião Azure, consulte a [visão geral.](bastion-overview.md)

Pode utilizar o Azure Bastion para se ligar a uma máquina virtual Linux utilizando o SSH. Pode utilizar as teclas Username/password e SSH para autenticação. Pode ligar-se ao vM com as teclas SSH utilizando qualquer um deles:

* Uma chave privada que você entra manualmente
* Um ficheiro que contém a informação da chave privada

A chave privada SSH deve estar num formato que comece com `"-----BEGIN RSA PRIVATE KEY-----"` e termine com `"-----END RSA PRIVATE KEY-----"`.

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que criou um anfitrião do Azure Bastion para a rede virtual em que o VM reside. Para mais informações, consulte [Create a Azure Bastion host](bastion-create-host-portal.md). Assim que o serviço Bastion for aprovisionado e implantado na sua rede virtual, pode usá-lo para se ligar a qualquer VM nesta rede virtual. 

Quando utiliza o Bastion para se ligar, assume que está a utilizar RDP para se ligar a um VM windows e SSH para se ligar aos seus VMs Linux. Para obter informações sobre a ligação a um VM do Windows, consulte [Connect a um VM - Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Funções necessárias

Para efazer uma ligação, são necessárias as seguintes funções:

* Papel do leitor na máquina virtual
* Papel do leitor no NIC com IP privado da máquina virtual
* Papel do leitor no recurso Azure Bastion

### <a name="ports"></a>Portas

Para ligar ao Linux VM via SSH, deve ter as seguintes portas abertas no seu VM:

* Porta de entrada: SSH (22)

## <a name="username"></a>Ligação: Utilização do nome de utilizador e da palavra-passe

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue para a máquina virtual a que pretende ligar, em seguida, clique em **Ligar** e selecione **Bastion** a partir do dropdown.

   ![Ligar](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três separadores – RDP, SSH e Bastion. Se a Bastion foi aprovisionada para a rede virtual, o separador Bastion está ativo por defeito. Se não disponibilizou bastião para a rede virtual, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Introduza o nome de utilizador e a palavra-passe para SSH na sua máquina virtual.
1. Clique no botão **Ligar** depois de introduzir a tecla.

## <a name="privatekey"></a>Ligação: Introduza manualmente uma chave privada

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue para a máquina virtual a que pretende ligar, em seguida, clique em **Ligar** e selecione **Bastion** a partir do dropdown.

   ![Ligar](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três separadores – RDP, SSH e Bastion. Se a Bastion foi aprovisionada para a rede virtual, o separador Bastion está ativo por defeito. Se não disponibilizou bastião para a rede virtual, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Introduza o nome de utilizador e selecione **A Chave Privada SSH**.
1. Introduza a sua chave privada na área de texto **SSH Private Key** (ou cola-a diretamente).
1. Clique no botão **Ligar** depois de introduzir a tecla.

## <a name="ssh"></a>Ligação: Utilizando um ficheiro de chave privada

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue para a máquina virtual a que pretende ligar, em seguida, clique em **Ligar** e selecione **Bastion** a partir do dropdown.

   ![Ligar](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três separadores – RDP, SSH e Bastion. Se a Bastion foi aprovisionada para a rede virtual, o separador Bastion está ativo por defeito. Se não disponibilizou bastião para a rede virtual, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Introduza o nome de utilizador e selecione **A Chave Privada SSH do Arquivo Local**.
1. Clique no botão **'Navegar'** (o ícone da pasta no ficheiro local).
1. Procure o ficheiro e, em seguida, clique em **Abrir**.
1. Clique em **Ligar** para ligar ao VM. Assim que clicar em Connect, sSH a esta máquina virtual abrirá diretamente no portal Azure. Esta ligação é superior a HTML5 utilizando a porta 443 no serviço Bastion sobre o IP privado da sua máquina virtual.

## <a name="next-steps"></a>Passos seguintes

Leia o [Bastião FAQ](bastion-faq.md)
