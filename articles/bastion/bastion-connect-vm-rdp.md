---
title: Ligue-se a um VM do Windows utilizando o Azure Bastion
description: Neste artigo, aprenda a ligar-se a uma Máquina Virtual Azure que executa o Windows utilizando o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744328"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Ligue-se a uma máquina virtual Windows usando Azure Bastion

Utilizando o Azure Bastion, pode ligar-se de forma segura e perfeita às suas máquinas virtuais sobre a SSL diretamente no portal Azure. Quando utiliza o Azure Bastion, os seus VMs não requerem um cliente, agente ou software adicional. Este artigo mostra-lhe como ligar-se aos seus VMs windows. Para obter informações sobre a ligação a um VM Linux, consulte [Connect to a VM usando Azure Bastion - Linux](bastion-connect-vm-ssh.md).

O Azure Bastion proporciona uma conectividade segura a todos os VMs na rede virtual em que é a provisionado. A utilização do Azure Bastion protege as suas máquinas virtuais de expor portas RDP/SSH ao mundo exterior, ao mesmo tempo que fornece acesso seguro utilizando RDP/SSH. Para obter mais informações, consulte [Descrição Geral](bastion-overview.md).

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que criou um anfitrião Azure Bastion para a rede virtual em que o VM está localizado. Uma vez que o serviço Bastion é aloque e implementado na sua rede virtual, pode usá-lo para ligar a qualquer VM da rede virtual. Para montar um anfitrião do Azure Bastion, consulte [criar um anfitrião do Azure Bastion.](bastion-create-host-portal.md)

### <a name="required-roles"></a>Funções necessárias

Para estabelecer uma ligação, são necessárias as seguintes funções:

* Papel do leitor na máquina virtual
* Função do leitor no NIC com IP privado da máquina virtual
* Papel do leitor no recurso Azure Bastion

### <a name="ports"></a>Portas

Para ligar ao Windows VM, tem de ter as seguintes portas abertas no seu VM Do Windows:

* Portas de entrada: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Ligar

1. Abra o [portal do Azure](https://portal.azure.com). Navegue para a máquina virtual a que pretende ligar, clique em **Ligar** e selecionar **Bastion** a partir do dropdown.

   ![Ligação VM](./media/bastion-connect-vm-rdp/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três separadores - RDP, SSH e Bastion. Se o Bastion foi previsto para a rede virtual, o separador Bastion está ativo por padrão. Se não forte Bastion para a rede virtual, pode clicar no link para configurar Bastion. Para obter instruções de configuração, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![separador de bastião](./media/bastion-connect-vm-rdp/bastion.png)
1. No separador Bastion, insira o nome de utilizador e a palavra-passe para a sua máquina virtual e, em seguida, clique em **Connect**. A ligação RDP a esta máquina virtual via Bastion abrir-se-á diretamente no portal Azure (sobre HTML5) utilizando a porta 443 e o serviço Bastion.

   ![Ligação RDP](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Passos seguintes

Leia as [FAQ do Bastião](bastion-faq.md)
