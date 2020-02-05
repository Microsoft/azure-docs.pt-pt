---
title: Ligue-se a um Linux VM usando o Bastião Azure  Microsoft Docs
description: Neste artigo, aprenda a ligar-se à Máquina Virtual Linux utilizando o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7fe1c2f74ca2a7b0fa4aefad934c45edd6f85a73
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990446"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Ligue usando o SSH a uma máquina virtual Linux usando o Bastião Azure

Este artigo mostra-lhe como obter sSH de forma segura e perfeita para os seus VMs Linux numa rede virtual Azure. Pode ligar a uma VM diretamente a partir do portal do Azure. Ao utilizar o Azure Bastion, as VMs não precisam de um cliente, agente ou software adicional. Para mais informações sobre o Bastião Azure, consulte a [visão geral.](bastion-overview.md)

Pode utilizar o Azure Bastion para se ligar a uma máquina virtual Linux utilizando o SSH. Pode utilizar as teclas Username/password e SSH para autenticação. Pode ligar-se ao vM com as teclas SSH utilizando qualquer um deles:

* Uma chave privada que você entra manualmente
* Um ficheiro que contém a informação da chave privada

A chave privada SSH deve estar num formato que comece com `"-----BEGIN RSA PRIVATE KEY-----"` e termine com `"-----END RSA PRIVATE KEY-----"`.

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que criou um anfitrião do Azure Bastion para a rede virtual em que o VM reside. Para mais informações, consulte [Create a Azure Bastion host](bastion-create-host-portal.md). Assim que o serviço Bastion for aprovisionado e implantado na sua rede virtual, pode usá-lo para se ligar a qualquer VM nesta rede virtual. 

Quando utiliza o Bastion para se ligar, assume que está a utilizar RDP para se ligar a um VM windows e SSH para se ligar aos seus VMs Linux. Para obter informações sobre a ligação a um VM do Windows, consulte [Connect a um VM - Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Funções necessárias

Para estabelecer uma conexão, as seguintes funções são necessárias:

* Função de leitor na máquina virtual
* Função de leitor na NIC com IP privado da máquina virtual
* Função de leitor no recurso de bastiões do Azure

### <a name="ports"></a>Portas

Para ligar ao Linux VM via SSH, deve ter as seguintes portas abertas no seu VM:

* Porta de entrada: SSH (22)


## <a name="username"></a>Ligação: Utilização do nome de utilizador e da palavra-passe

1.   Abra o [Portal do Azure](https://portal.azure.com). Navegue na máquina virtual a que pretende ligar e, em seguida, clique em **Connect**. O VM deve ser uma máquina virtual Linux quando utilizar uma ligação SSH.
1. Depois de clicar em Connect, aparece uma barra lateral que tem três separadores – RDP, SSH e Bastion. Se a Bastion foi aprovisionada para a rede virtual, o separador Bastion está ativo por defeito. Se não disponibilizou bastião para a rede virtual, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![Ligação VM](./media/bastion-connect-vm-ssh/bastion.png)
1. Introduza o nome de utilizador e a palavra-passe para SSH na sua máquina virtual.
1. Clique no botão **Ligar** depois de introduzir a tecla.

## <a name="privatekey"></a>Ligação: Introduza manualmente uma chave privada

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue na máquina virtual a que pretende ligar e, em seguida, clique em **Connect**. O VM deve ser uma máquina virtual Linux quando utilizar uma ligação SSH.
1. Depois de clicar em Connect, aparece uma barra lateral que tem três separadores – RDP, SSH e Bastion. Se a Bastion foi aprovisionada para a rede virtual, o separador Bastion está ativo por defeito. Se não disponibilizou bastião para a rede virtual, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![Ligação VM](./media/bastion-connect-vm-ssh/bastion.png)
1. Introduza o nome de utilizador e selecione **A Chave Privada SSH**.
1. Introduza a sua chave privada na área de texto **SSH Private Key** (ou cola-a diretamente).
1. Clique no botão **Ligar** depois de introduzir a tecla.

## <a name="ssh"></a>Ligação: Utilizando um ficheiro de chave privada

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue na máquina virtual a que pretende ligar e, em seguida, clique em **Connect**. O VM deve ser uma máquina virtual Linux quando utilizar uma ligação SSH.

   ![Ligação VM](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em Connect, aparece uma barra lateral que tem três separadores – RDP, SSH e Bastion. Se a Bastion foi aprovisionada para a rede virtual, o separador Bastion está ativo por defeito. Se não disponibilizou bastião para a rede virtual, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![Ligação VM](./media/bastion-connect-vm-ssh/bastion.png)
1. Introduza o nome de utilizador e selecione **A Chave Privada SSH do Arquivo Local**.
1. Clique no botão **'Navegar'** (o ícone da pasta no ficheiro local).
1. Procure o ficheiro e, em seguida, clique em **Abrir**.
1. Clique em **Ligar** para ligar ao VM. Assim que clicar em Connect, sSH a esta máquina virtual abrirá diretamente no portal Azure. Esta ligação é superior a HTML5 utilizando a porta 443 no serviço Bastion sobre o IP privado da sua máquina virtual.

## <a name="next-steps"></a>Passos seguintes

Leia o [Bastião FAQ](bastion-faq.md)
