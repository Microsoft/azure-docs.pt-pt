---
title: Ligar a uma VM do Windows com o Azure Bastion | Documentos da Microsoft
description: Neste artigo, saiba como ligar a uma Máquina Virtual do Azure com o Windows utilizando o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: c8a4b09a27325f31e548d1b345b2932c6ab6315c
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191892"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>Ligar a uma máquina de virtual de Windows através do Azure Bastion (pré-visualização)

Este artigo mostra como a forma segura e RDP para as VMs do Windows no Azure virtual de rede através do Azure Bastion. Pode ligar a uma VM diretamente a partir do portal do Azure. Ao utilizar o Azure Bastion, as VMs não precisam de um cliente, agente ou software adicional. Para obter mais informações sobre Bastion do Azure, consulte a [descrição geral](bastion-overview.md).

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que configurou um anfitrião de bastião do Azure para a rede virtual em que a VM reside. Para obter mais informações, consulte [criar um anfitrião de bastião de Azure](bastion-create-host-portal.md). Assim que o serviço de Bastion é aprovisionado e implementado na sua rede virtual, pode usá-lo para ligar a qualquer VM nesta rede virtual. Nesta pré-visualização, Bastion pressupõe que está a utilizar o RDP para ligar a uma VM do Windows e SSH para ligar às suas VMs do Linux. Para obter informações sobre a ligação a uma VM do Linux, consulte [ligar a uma VM - Linux](bastion-connect-vm-ssh.md).

Para estabelecer uma ligação, as seguintes funções são necessárias:

* Função de leitor na máquina virtual
* Função de leitor na NIC com o IP privado da máquina virtual
* Função de leitor do recurso de Bastion do Azure

## <a name="rdp"></a>Estabelecer ligação utilizando RDP

1. Na [portal do Azure](https://aka.ms/BastionHost) para a pré-visualização de Bastion, navegue para a máquina virtual que deseja se conectar, em seguida, clique em **Connect**. A VM deve ser uma máquina virtual do Windows ao utilizar uma ligação RDP.

    ![Ligação da VM](./media/bastion-connect-vm-rdp/connect.png)

1. Depois de clicar em ligar, é apresentada uma barra lateral que tem três separadores: Bastion, RDP e SSH. Se tiver Bastion foi aprovisionado para a rede virtual, no separador de Bastion está ativo por predefinição. Se não o tiver aprovisionado Bastion para a rede virtual, pode clicar na ligação para configurar Bastion. Para obter instruções de configuração, consulte [Bastion configurar](bastion-create-host-portal.md). Se não vir **Bastion** listada, não abriu o portal de pré-visualização. Abra o portal usando essa [ligação de pré-visualização](https://aka.ms/BastionHost).

    ![Ligação da VM](./media/bastion-connect-vm-rdp/bastion.png)

1. No separador de Bastion, o nome de utilizador e palavra-passe para a máquina virtual, em seguida, clique em **Connect**. A ligação RDP a esta máquina virtual através de Bastion será aberto diretamente no portal do Azure (ao longo do HTML5) com a porta 443 e o serviço de Bastion.

    ![Ligação da VM](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Passos Seguintes

Leia o [Bastion FAQ](bastion-faq.md)