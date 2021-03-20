---
title: Ligue-se a um conjunto de escala de máquina virtual do Windows utilizando o Azure Bastion | Microsoft Docs
description: Neste artigo, aprenda a ligar-se a um conjunto de escala de máquina virtual Azure utilizando Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: charwen
ms.openlocfilehash: 001d2ff6789ec4cfcc391171f0859b67ab1ee0a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077784"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Ligue-se a um conjunto de escala de máquina virtual usando Azure Bastion

Este artigo mostra-lhe como obter RDP de forma segura e perfeita para o seu conjunto de escala de máquina virtual Windows numa rede virtual Azure utilizando O Azure Bastion. Pode ligar-se a uma placa de escala de máquina virtual diretamente a partir do portal Azure. Ao utilizar o Azure Bastion, os VM não requerem um cliente, agente ou software adicional. Para mais informações sobre O Bastião Azure, consulte a [Visão Geral.](bastion-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que criou um anfitrião Azure Bastion para a rede virtual em que reside o conjunto de escalas de máquina virtual. Para mais informações, consulte [Criar um anfitrião do Azure Bastion.](./tutorial-create-host-portal.md) Uma vez que o serviço Bastion é aloque e implementado na sua rede virtual, pode usá-lo para ligar a uma placa de escala de máquina virtual definida nesta rede virtual. O Bastion assume que está a utilizar o RDP para ligar a um conjunto de escala de máquina virtual do Windows e sSH para ligar ao conjunto de balanças de máquina virtual Linux. Para obter informações sobre a ligação a um Linux VM, consulte [Connect to a VM - Linux](bastion-connect-vm-ssh.md).

## <a name="connect-using-rdp"></a><a name="rdp"></a>Ligar usando RDP

1. Abra o [portal do Azure](https://portal.azure.com). Navegue para o conjunto de escala de máquina virtual a que pretende ligar.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Navegue para a instância de conjunto de escala de máquina virtual a que pretende ligar e, em seguida, selecione **Connect**. Ao utilizar uma ligação RDP, o conjunto de escala de máquina virtual deve ser um conjunto de balança de máquina virtual do Windows.

   ![conjunto de escala de máquina virtual](./media/bastion-connect-vm-scale-set/2.png)
3. Depois de selecionar **Connect,** aparece uma barra lateral que tem três separadores - RDP, SSH e Bastion. Selecione o **separador Bastion** da barra lateral. Se não forte Bastion para a rede virtual, pode selecionar o link para configurar Bastion. Para obter instruções de configuração, consulte [Configure Bastion](./tutorial-create-host-portal.md).

   ![Separador de bastião](./media/bastion-connect-vm-scale-set/3.png)
4. No separador Bastion, introduza o nome de utilizador e a palavra-passe para o seu conjunto de escala de máquina virtual e, em seguida, selecione **Connect**.

   ![ligar](./media/bastion-connect-vm-scale-set/4.png)
5. A ligação RDP a esta máquina virtual via Bastion abrir-se-á diretamente no portal Azure (sobre HTML5) utilizando a porta 443 e o serviço Bastion.

## <a name="next-steps"></a>Passos seguintes

Leia as [FAQ do Bastião.](bastion-faq.md)