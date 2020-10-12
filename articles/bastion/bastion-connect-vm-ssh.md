---
title: Ligue-se a um Linux VM usando Azure Bastion
description: Neste artigo, aprenda a ligar-se à Máquina Virtual Linux utilizando o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: f9bc06e58fcbd93fba0c92e91b51503239b5a951
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90980783"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Ligue o uso do SSH a uma máquina virtual Linux utilizando o Azure Bastion

Este artigo mostra-lhe como fazer SSH de forma segura e perfeita aos seus VMs Linux numa rede virtual Azure. Pode ligar-se a um VM diretamente a partir do portal Azure. Ao utilizar o Azure Bastion, os VM não requerem um cliente, agente ou software adicional. Para mais informações sobre O Bastião Azure, consulte a [Visão Geral.](bastion-overview.md)

Pode utilizar o Azure Bastion para ligar-se a uma máquina virtual Linux utilizando sSH. Pode utilizar as chaves username/password e SSH para autenticação. Pode ligar-se ao seu VM com as teclas SSH utilizando:

* Uma chave privada que você entra manualmente
* Um ficheiro que contém a informação chave privada

A chave privada SSH deve estar num formato que comece  `"-----BEGIN RSA PRIVATE KEY-----"` e termine com `"-----END RSA PRIVATE KEY-----"` .

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que criou um anfitrião Azure Bastion para a rede virtual em que reside o VM. Para mais informações, consulte [Criar um anfitrião do Azure Bastion.](bastion-create-host-portal.md) Uma vez que o serviço Bastion é aloque e implementado na sua rede virtual, pode usá-lo para ligar a qualquer VM nesta rede virtual. 

Quando utiliza o Bastion para ligar, assume que está a utilizar RDP para ligar a um VM do Windows e SSH para ligar aos seus VMs Linux. Para obter informações sobre a ligação a um VM do Windows, consulte [Connect a um VM - Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Funções necessárias

Para estabelecer uma ligação, são necessárias as seguintes funções:

* Papel do leitor na máquina virtual
* Função do leitor no NIC com IP privado da máquina virtual
* Papel do leitor no recurso Azure Bastion

### <a name="ports"></a>Portas

Para ligar ao Linux VM via SSH, tem de ter as seguintes portas abertas no seu VM:

* Porta de entrada: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Conecte-se: Usando o nome de utilizador e a palavra-passe

1. Abra o [portal do Azure](https://portal.azure.com). Navegue para a máquina virtual a que pretende ligar, clique em **Ligar** e selecionar **Bastion** a partir do dropdown.

   ![A screenshot mostra a visão geral de uma máquina virtual no portal Azure com o Connect selecionado.](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três separadores - RDP, SSH e Bastion. Se o Bastion foi previsto para a rede virtual, o separador Bastion está ativo por padrão. Se não forte Bastion para a rede virtual, consulte o [Configure Bastion](bastion-create-host-portal.md).

   ![A screenshot mostra a caixa de diálogo de máquina virtual Connect com a BASTION selecionada.](./media/bastion-connect-vm-ssh/bastion.png)
1. Introduza o nome de utilizador e a palavra-passe para SSH na sua máquina virtual.
1. Clique no botão **Conecte-se** depois de introduzir a tecla.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Conecte-se: Introduza manualmente uma chave privada

1. Abra o [portal do Azure](https://portal.azure.com). Navegue para a máquina virtual a que pretende ligar, clique em **Ligar** e selecionar **Bastion** a partir do dropdown.

   ![A screenshot mostra a visão geral de uma máquina virtual no portal Azure com o Connect selecionado.](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três separadores - RDP, SSH e Bastion. Se o Bastion foi previsto para a rede virtual, o separador Bastion está ativo por padrão. Se não forte Bastion para a rede virtual, consulte o [Configure Bastion](bastion-create-host-portal.md).

   ![A screenshot mostra a caixa de diálogo de máquina virtual Connect com a BASTION selecionada.](./media/bastion-connect-vm-ssh/bastion.png)
1. Introduza o nome de utilizador e selecione **a Chave Privada SSH**.
1. Introduza a sua chave privada na área de texto **SSH Private Key** (ou cole-a diretamente).
1. Clique no botão **Conecte-se** depois de introduzir a tecla.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Conecte-se: Utilizar um ficheiro chave privado

1. Abra o [portal do Azure](https://portal.azure.com). Navegue para a máquina virtual a que pretende ligar, clique em **Ligar** e selecionar **Bastion** a partir do dropdown.

   ![A screenshot mostra a visão geral de uma máquina virtual no portal Azure com o Connect selecionado.](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três separadores - RDP, SSH e Bastion. Se o Bastion foi previsto para a rede virtual, o separador Bastion está ativo por padrão. Se não forte Bastion para a rede virtual, consulte o [Configure Bastion](bastion-create-host-portal.md).

   ![A screenshot mostra a caixa de diálogo de máquina virtual Connect com a BASTION selecionada.](./media/bastion-connect-vm-ssh/bastion.png)
1. Introduza o nome de utilizador e selecione **a chave privada SSH a partir do Arquivo Local.**
1. Clique no botão **Procurar** (o ícone de pasta no ficheiro local).
1. Navegue pelo ficheiro e, em seguida, clique em **Abrir**.
1. Clique **em Ligar** para ligar ao VM. Assim que clicar em Connect, o SSH a esta máquina virtual abrir-se-á diretamente no portal Azure. Esta ligação é mais de HTML5 utilizando a porta 443 no serviço Bastion sobre o IP privado da sua máquina virtual.

## <a name="next-steps"></a>Passos seguintes

Leia as [FAQ do Bastião](bastion-faq.md)
