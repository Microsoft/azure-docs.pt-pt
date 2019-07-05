---
title: Ligar a uma VM com Linux através do Azure Bastion | Documentos da Microsoft
description: Neste artigo, saiba como ligar a Máquina Virtual do Linux com o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 69548541d16db95f633400808f72aebaf59cff08
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477771"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>Ligar com SSH para uma máquina virtual do Linux utilizar Bastion do Azure (pré-visualização)

Este artigo mostra-lhe como de forma segura e encaminhar o SSH para as VMs do Linux numa rede virtual do Azure. Pode ligar a uma VM diretamente a partir do portal do Azure. Ao utilizar o Azure Bastion, as VMs não precisam de um cliente, agente ou software adicional. Para obter mais informações sobre Bastion do Azure, consulte a [descrição geral](bastion-overview.md).

Pode utilizar o Azure Bastion para ligar a uma máquina virtual do Linux através de SSH. Pode utilizar o nome de utilizador/palavra-passe e chaves SSH para autenticação. Pode ligar à sua VM com chaves SSH, utilizando um:

* Uma chave privada que introduzir manualmente
* Um ficheiro que contém as informações da chave privadas

A chave privada SSH tem de estar num formato que começa com `"-----BEGIN RSA PRIVATE KEY-----"` e termina com `"-----END RSA PRIVATE KEY-----"`.

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que configurou um anfitrião de bastião do Azure para a rede virtual em que a VM reside. Para obter mais informações, consulte [criar um anfitrião de bastião de Azure](bastion-create-host-portal.md). Assim que o serviço de Bastion é aprovisionado e implementado na sua rede virtual, pode usá-lo para ligar a qualquer VM nesta rede virtual. Nesta pré-visualização, ao utilizar Bastion para ligar, parte do princípio que está a utilizar o RDP para ligar a uma VM do Windows e SSH para ligar às suas VMs do Linux.

Para estabelecer uma ligação, as seguintes funções são necessárias:

* Função de leitor na máquina virtual
* Função de leitor na NIC com o IP privado da máquina virtual
* Função de leitor do recurso de Bastion do Azure

## <a name="username"></a>Ligue-se: Com o nome de utilizador e palavra-passe


1.  Uso [esta ligação](https://aka.ms/BastionHost) para abrir a página de portal de pré-visualização da Bastion do Azure. Navegue para a máquina virtual que deseja se conectar, em seguida, clique em **Connect**. A VM deve ser uma máquina virtual Linux ao utilizar uma ligação SSH.
1. Depois de clicar em ligar, é apresentada uma barra lateral que tem três separadores: Bastion, RDP e SSH. Se tiver Bastion foi aprovisionado para a rede virtual, no separador de Bastion está ativo por predefinição. Se não o tiver aprovisionado Bastion para a rede virtual, veja [Bastion configurar](bastion-create-host-portal.md). Se não vir **Bastion** listada, não abriu o portal de pré-visualização. Abra o portal com [esta ligação](https://aka.ms/BastionHost).

      ![Ligação da VM](./media/bastion-connect-vm-ssh/bastion.png)

1. Introduza o nome de utilizador e palavra-passe para SSH à máquina virtual.
1. Clique em **Connect** botão depois de introduzir a chave.

## <a name="privatekey"></a>Ligue-se: Introduzir manualmente uma chave privada

1.  Uso [esta ligação](https://aka.ms/BastionHost) para abrir a página de portal de pré-visualização da Bastion do Azure. Navegue para a máquina virtual que deseja se conectar, em seguida, clique em **Connect**. A VM deve ser uma máquina virtual Linux ao utilizar uma ligação SSH.
1. Depois de clicar em ligar, é apresentada uma barra lateral que tem três separadores: Bastion, RDP e SSH. Se tiver Bastion foi aprovisionado para a rede virtual, no separador de Bastion está ativo por predefinição. Se não o tiver aprovisionado Bastion para a rede virtual, veja [Bastion configurar](bastion-create-host-portal.md). Se não vir **Bastion** listada, não abriu o portal de pré-visualização. Abra o portal com [esta ligação](https://aka.ms/BastionHost).

      ![Ligação da VM](./media/bastion-connect-vm-ssh/bastion.png)

1. Introduza o nome de utilizador e selecione **SSH chave privada**.
1. Introduza a chave privada para a área de texto **SSH chave privada** (ou cole-o diretamente).
1. Clique em **Connect** botão depois de introduzir a chave.

## <a name="ssh"></a>Ligue-se: Utilizar um ficheiro de chave privada

1.  Uso [esta ligação](https://aka.ms/BastionHost) para abrir a página de portal de pré-visualização da Bastion do Azure. Navegue para a máquina virtual que deseja se conectar, em seguida, clique em **Connect**. A VM deve ser uma máquina virtual Linux ao utilizar uma ligação SSH.

    ![Ligação da VM](./media/bastion-connect-vm-ssh/connect.png)

1. Depois de clicar em ligar, é apresentada uma barra lateral que tem três separadores: Bastion, RDP e SSH. Se tiver Bastion foi aprovisionado para a rede virtual, no separador de Bastion está ativo por predefinição. Se não o tiver aprovisionado Bastion para a rede virtual, veja [Bastion configurar](bastion-create-host-portal.md). Se não vir **Bastion** listada, não abriu o portal de pré-visualização. Abra o portal com [esta ligação](https://aka.ms/BastionHost).

    ![Ligação da VM](./media/bastion-connect-vm-ssh/bastion.png)

1. Introduza o nome de utilizador e selecione **SSH chave privada do ficheiro Local**.
1. Clique nas **procurar** botão (o ícone de pasta no arquivo local).
1. Navegar para o ficheiro, em seguida, clique em **aberto**.
1. Clique em **Connect** para ligar à VM. Assim que clicar em ligar, SSH a esta máquina virtual será aberto diretamente no portal do Azure. Esta ligação é efetuada através do HTML5 através da porta 443 no serviço de Bastion através de IP privado da sua máquina virtual.

## <a name="next-steps"></a>Passos Seguintes

Leia o [Bastion FAQ](bastion-faq.md)
