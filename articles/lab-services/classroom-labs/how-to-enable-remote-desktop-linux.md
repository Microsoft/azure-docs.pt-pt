---
title: Ativar o ambiente de trabalho remoto para linux nos Serviços de Laboratório Azure  Microsoft Docs
description: Aprenda a ativar o ambiente de trabalho remoto para máquinas virtuais Linux num laboratório em Serviços de Laboratório Azure.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360646"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Ativar o ambiente de trabalho remoto para máquinas virtuais Linux em um laboratório em Serviços de Laboratório Azure
Este artigo mostra-lhe como fazer as seguintes tarefas:

- Ativar o ambiente de trabalho remoto para Linux VM
- Como o professor pode ligar-se ao modelo VM através da Ligação remota de ambiente de trabalho (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Ativar o ambiente de trabalho remoto para Linux VM
Durante a criação do laboratório, os professores podem permitir **a ligação remota de ambiente** de trabalho para imagens **linux.** A opção **Enable Remote Desktop Connection** é mostrada quando uma imagem Linux é selecionada para o modelo. Quando esta opção está ativada, os professores podem ligar-se ao modelo VM e vMs dos alunos via RDP (Ambiente de Trabalho Remoto). 

![Ativar a ligação remota para o ambiente de trabalho para uma imagem linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Na caixa de mensagens de ligação à **secretária de trabalho remota ativa,** selecione Continue com o Ambiente de Trabalho **Remoto**. 

![Ativar a ligação remota para o ambiente de trabalho para uma imagem linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Ativar a **ligação remota** de ambiente de trabalho apenas abre a porta **RDP** nas máquinas Linux. Se o RDP já estiver instalado e configurado na imagem da máquina virtual (por exemplo: Ubuntu Data Science Virtual Machine image), você/estudantes podem ligar-se a VMs via RDP sem seguir quaisquer passos adicionais.
> 
> Se a imagem VM não tiver RDP instalada e configurada, precisa de ligar à máquina Linux utilizando sSH pela primeira vez e instalar pacotes RDP e GUI para que possa ligar-se à máquina Linux utilizando RDP mais tarde. Para mais informações, consulte Instalar e configurar o [Ambiente de Trabalho Remoto para ligar a um Linux VM em Azure](../../virtual-machines/linux/use-remote-desktop.md). Depois, publica a imagem para que os alunos possam fazer RDP nos VMs do aluno Linux. 

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
Atualmente, a ligação remota de ambiente de trabalho é suportada para os seguintes sistemas operativos:

- abrir Salto SUSE 42.3
- Baseado em CentOS 7.5
- Debian 9 "Esticar"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Ligar à VM do modelo 
Os professores devem ligar-se ao modelo VM usando primeiro o SSH e instalar pacotes RDP e GUI nele. Em seguida, os professores podem usar RDP para ligar ao modelo VM: 

1. Se vir o **modelo Personalizar** na barra de ferramentas, selecione-o. Em seguida, selecione **Continuar** na caixa de diálogo do **modelo Personalizar.** Esta ação inicia o modelo VM.  

    ![Modelo de personalizar](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Depois do vM do modelo ter começado, pode selecionar o **modelo De Ligar** e, em seguida, ligar através do **SSH** na barra de ferramentas. 

    ![Ligue-se ao modelo via RDP após a criação do laboratório](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Veja o seguinte Ligar à sua caixa de diálogo **virtual.** Selecione o botão **Copiar** ao lado da caixa de texto para copiá-lo para a área de recortes. Guarde a cadeia de ligação SSH. Utilize esta cadeia de ligação a partir de um terminal SSH (como [putty)](https://www.putty.org/)para ligar à máquina virtual.
 
    ![Cadeia de ligação SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Instale pacotes RDP e GUI para que possa ligar-se à máquina Linux utilizando RDP mais tarde. Para mais informações, consulte Instalar e configurar o [Ambiente de Trabalho Remoto para ligar a um Linux VM em Azure](../../virtual-machines/linux/use-remote-desktop.md). Depois, publica a imagem para que os alunos possam fazer RDP nos VMs do aluno Linux.
5. Depois de instaladas estas embalagens, pode utilizar o **Modelo Connect para** a barra de ferramentas e, em seguida, selecionar Ligar via **RDP** para ligar ao modelo VM via RDP. Guarde o ficheiro RDP e use-o para ligar ao modelo VM via RDP. 

## <a name="next-steps"></a>Passos Seguintes
Depois de um instrutor ativar a função de ligação remota de ambiente de trabalho, os alunos podem ligar-se aos seus VMs via RDP/SSH. Para mais informações, consulte Utilize o [ambiente de trabalho remoto para VMs linux num laboratório de sala de aula](how-to-use-remote-desktop-linux-student.md). 