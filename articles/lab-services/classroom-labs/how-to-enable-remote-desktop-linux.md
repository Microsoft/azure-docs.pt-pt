---
title: Habilitar a área de trabalho remota para Linux no Azure Lab Services | Microsoft Docs
description: Saiba como habilitar a área de trabalho remota para máquinas virtuais Linux em um laboratório no Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 65a77b8243e7afc8d858360d3d3be86f44e6b67e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332154"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Habilitar a área de trabalho remota para máquinas virtuais Linux em um laboratório no Azure Lab Services
Este artigo mostra como realizar as seguintes tarefas:

- Habilitar área de trabalho remota para VM Linux
- Como o professor pode se conectar à VM do modelo via Conexão de Área de Trabalho Remota (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Habilitar área de trabalho remota para VM Linux
Durante a criação do laboratório, os professores podem habilitar a **conexão de área de trabalho remota** para imagens do **Linux** . A opção **habilitar conexão de área de trabalho remota** é mostrada quando uma imagem do Linux é selecionada para o modelo. Quando essa opção está habilitada, os professores podem se conectar à VM de modelo e VMs de estudante via RDP (Área de Trabalho Remota). 

![Habilitar a conexão de área de trabalho remota para uma imagem do Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Na caixa de mensagem **habilitando conexão de área de trabalho remota** , selecione **continuar com área de trabalho remota**. 

![Habilitar a conexão de área de trabalho remota para uma imagem do Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Habilitar a **conexão de área de trabalho remota** abre apenas a porta **RDP** em computadores Linux. Se o RDP já estiver instalado e configurado na imagem da máquina virtual (por exemplo: Ubuntu Máquina Virtual de Ciência de Dados Image), você/alunos poderão se conectar a VMs via RDP sem seguir nenhuma etapa adicional.
> 
> Se a imagem da VM não tiver o RDP instalado e configurado, você precisará se conectar ao computador Linux usando o SSH pela primeira vez e instalar os pacotes RDP e GUI para que você/alunos possam se conectar ao computador Linux usando o RDP mais tarde. Para obter mais informações, consulte [instalar e configurar o área de trabalho remota para se conectar a uma VM do Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md). Em seguida, você publica a imagem para que os alunos possam RDP nas VMs do Linux do aluno. 

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
Atualmente, a conexão de área de trabalho remota tem suporte para os seguintes sistemas operacionais:

- openSUSE Leap 42,3
- Baseado em CentOS 7,5
- "Stretch" do Debian 9
- Ubuntu Server 16, 4 LTS

## <a name="connect-to-the-template-vm"></a>Ligar à VM do modelo 
Os professores devem se conectar à VM de modelo usando o SSH primeiro e instalar os pacotes RDP e GUI nela. Em seguida, os professores podem usar o RDP para se conectar à VM do modelo: 

1. Se você vir **Personalizar modelo** na barra de ferramentas, selecione-o. Em seguida, selecione **continuar** na caixa de diálogo **Personalizar modelo** . Essa ação inicia a VM do modelo.  

    ![Personalizar modelo](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Depois que a VM de modelo for iniciada, você poderá selecionar **conectar modelo** e, em seguida, **conectar-se via SSH** na barra de ferramentas. 

    ![Conectar-se ao modelo via RDP após a criação do laboratório](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Você verá a caixa de diálogo **conectar-se à sua máquina virtual** a seguir. Selecione o botão **copiar** ao lado da caixa de texto para copiá-la para a área de transferência. Salve a cadeia de conexão SSH. Use essa cadeia de conexão de um terminal SSH ( [como uma](https://www.putty.org/)reseqüência) para se conectar à máquina virtual.
 
    ![Cadeia de conexão SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Instale pacotes RDP e GUI para que você/alunos possam se conectar ao computador Linux usando o RDP mais tarde. Para obter mais informações, consulte [instalar e configurar o área de trabalho remota para se conectar a uma VM do Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md). Em seguida, você publica a imagem para que os alunos possam RDP nas VMs do Linux do aluno.
5. Depois que esses pacotes são instalados, você pode usar o **modelo conectar-se a** na barra de ferramentas e, em seguida, selecionar **conectar via RDP** para se conectar à VM de modelo via RDP. Salve o arquivo RDP e use-o para se conectar à VM de modelo via RDP. 

## <a name="next-steps"></a>Passos seguintes
Depois que um instrutor habilitou o recurso de conexão de área de trabalho remota, os alunos podem se conectar às suas VMs por meio de RDP/SSH. Para obter mais informações, consulte [usar a área de trabalho remota para VMs do Linux em um laboratório de sala de aula](how-to-use-remote-desktop-linux-student.md). 