---
title: Ativar o ambiente de trabalho remoto para o Linux no Azure Lab Services | Documentos da Microsoft
description: Saiba como ativar o ambiente de trabalho remoto para máquinas virtuais do Linux num laboratório no Azure Lab Services.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 9ad6f82d7b9bd7c4957df1dd37d0f2ddf7462e9e
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410958"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Ativar e utilizar o ambiente de trabalho remoto para máquinas virtuais do Linux num laboratório no Azure Lab Services
Este artigo mostra-lhe como fazer as seguintes tarefas:

- Ativar o ambiente de trabalho remoto para VM do Linux
- Como professor pode ligar-se ao modelo de VM através de ligação de ambiente de trabalho remoto (RDP).
- Como os estudantes ligar para o aluno VM através de RDP

## <a name="enable-remote-desktop-for-linux-vm"></a>Ativar o ambiente de trabalho remoto para VM do Linux
Durante a criação de laboratório, podem ativar a professores **conexão de área de trabalho remoto** para **Linux** imagens. O **ativar a ligação de ambiente de trabalho remota** opção é apresentada quando uma imagem de Linux está selecionada para o modelo. Quando esta opção está ativada, professores podem ligar-se ao modelo de VM e VMs de estudante através de RDP (ambiente de trabalho remoto). 

![Ativar a ligação de ambiente de trabalho remoto para uma imagem do Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

> [!IMPORTANT] 
> Habilitando **conexão de área de trabalho remoto** abre apenas a **RDP** porta nas máquinas do Linux. , Como um professor, ligar à máquina do Linux através de SSH pela primeira vez e instalar os pacotes RDP e GUI para que possam ligar à máquina Linux utilizar RDP mais tarde. Em seguida, **publicar** na imagem para que os estudantes possam RDP para o aluno VMs do Linux. 

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
Atualmente, a conexão de área de trabalho remoto é suportada para os seguintes sistemas operativos:

- openSUSE Leap 42.3
- 7.5 baseada em centOS
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Professores ligar para o modelo de VM através de RDP
Professores tem de se ligar ao modelo de VM com o SSH primeiro e instalar os pacotes RDP e GUI no mesmo. Em seguida, os professores podem utilizar os seguintes passos para ligar as VMs do Linux através de RDP: 

Verá o **ambiente de trabalho remoto** opção para ligar ao modelo de VM no momento da criação do laboratório. 

![Ligar ao modelo através do RDP no momento da criação](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Verá o **ambiente de trabalho remoto** opção na homepage do laboratório depois de criar o laboratório e o modelo de VM é iniciada. Inicie o modelo de VM, se esta não está a ser iniciado. 

![Ligar ao modelo através de RDP após a criação do laboratório](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Quando seleciona a **RDP** opção, transfere um ficheiro RDP. Abra-o para ligar à máquina do Linux. 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Professores ligar a um VM de estudante através de RDP
Proprietário de um laboratório (Professor/professor) pode ligar a um VM de estudante alternando para o **máquinas virtuais** ver e selecionar o **ligar** ícone. Antes disso, tem de professores **publicar** a imagem de modelo com os pacotes RDP e GUI instalado nele. 

![Professores ligar para a VM de estudante](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

## <a name="students-connecting-to-the-student-vm"></a>Estudantes a ligar a VM de estudante
Estudante pode RDP para as respetivas VMs do Linux, depois do proprietário de laboratório (Professor/professor) **publica** o modelo de VM com os pacotes RDP e GUI instalado na máquina. Eis os passos: 

1. Quando um estudante inicia sessão no portal Labs do diretamente (`https://labs.azure.com`) ou através de uma ligação de registo (`https://labs.azure.com/register/<registrationCode>`), um mosaico para cada um do aluno tem acesso a é apresentado. 
2. No mosaico, selecione **iniciar** se a VM é parada. 
3. Selecione **Ligar**. Esta ação transfere o ficheiro RDP da sessão no seu computador. Guarde-o e abra-a para ligar à máquina do Linux através de RDP. 

    ![Baixe o estudante VM - RDP](../media/how-to-enable-remote-desktop-linux/student-rdp-download.png)

    Ainda pode ligar à VM do Linux utilizando SSH. Selecione **... (reticências)**  para ver a opção de SSH. 
    
    ![Estudante VM - SSH](../media/how-to-enable-remote-desktop-linux/student-ssh.png)

    Copie e guarde a cadeia de ligação SSH sobre os **ligar à máquina virtual** caixa de diálogo. Utilize esta cadeia de ligação de um terminal SSH (como [Putty](https://www.putty.org/)) para ligar à máquina virtual. 

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como administrador, criar e gerir contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerir laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como um utilizador de laboratório, acessar os laboratórios de sala de aula](how-to-use-classroom-lab.md)

