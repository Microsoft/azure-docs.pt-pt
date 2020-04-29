---
title: Ligue-se a um conjunto de máquinas virtuais do Windows utilizando o Bastião Azure [ Microsoft Docs
description: Neste artigo, aprenda a ligar-se a um conjunto de máquinas virtuais Azure utilizando o Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 290a20fcd827841c24983f3bdd54b6db8e154462
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619348"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Ligue-se a um conjunto de escala de máquina virtual usando o Bastião Azure

Este artigo mostra-lhe como de forma segura e perfeita rdp para a sua escala de máquina virtual Windows numa rede virtual Azure utilizando o Azure Bastion. Pode ligar-se a uma instância de conjunto de escala de máquina virtual diretamente do portal Azure. Ao utilizar o Azure Bastion, os VMs não requerem um cliente, agente ou software adicional. Para mais informações sobre o Bastião Azure, consulte a [visão geral.](bastion-overview.md)

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que criou um anfitrião do Azure Bastion para a rede virtual em que reside o conjunto de escala de máquina virtual. Para mais informações, consulte [Create a Azure Bastion host](bastion-create-host-portal.md). Uma vez que o serviço Bastion é aprovisionado e implantado na sua rede virtual, pode usá-lo para ligar a uma instância de conjunto de escala de máquina virtual nesta rede virtual. A Bastion assume que está a utilizar RDP para se ligar a um conjunto de escala de máquinas virtuais do Windows e sSH para se ligar ao conjunto de escala de máquinavirtual Linux. Para obter informações sobre a ligação a um VM Linux, consulte [Connect to a VM - Linux](bastion-connect-vm-ssh.md).

## <a name="connect-using-rdp"></a><a name="rdp"></a>Conecte-se usando RDP

1. Abra o [portal Azure.](https://portal.azure.com) Navegue para o conjunto de escala de máquina virtual a que pretende ligar.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Navegue para a instância de conjunto de escala de máquina virtual a que pretende ligar e, em seguida, selecione **Connect**. Ao utilizar uma ligação RDP, o conjunto de escala de máquina virtual deve ser um conjunto de escala de máquina virtual do Windows.

   ![conjunto de escala de máquina virtual](./media/bastion-connect-vm-scale-set/2.png)
3. Depois de selecionar **Connect**, aparece uma barra lateral que tem três separadores – RDP, SSH e Bastion. Selecione o separador **Bastion** da barra lateral. Se não disponibilizou a Bastion para a rede virtual, pode selecionar o link para configurar o Bastion. Para instruções de configuração, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![Guia de bastião](./media/bastion-connect-vm-scale-set/3.png)
4. No separador Bastion, introduza o nome de utilizador e a palavra-passe para o conjunto de escala de máquina virtual e, em seguida, selecione **Connect**.

   ![ligar](./media/bastion-connect-vm-scale-set/4.png)
5. A ligação RDP a esta máquina virtual via Bastion abrirá diretamente no portal Azure (mais HTML5) utilizando a porta 443 e o serviço Bastião.

## <a name="next-steps"></a>Passos seguintes

Leia o [Bastião FAQ.](bastion-faq.md)