---
title: Utilize o ambiente de trabalho remoto para linux nos Serviços de Laboratório Azure [ Microsoft Docs
description: Aprenda a usar o ambiente de trabalho remoto para máquinas virtuais Linux num laboratório em Serviços de Laboratório Azure.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 8ccad0698ea6560dd183cacc71f5f3a644e8220c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588110"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Utilize o ambiente de trabalho remoto para máquinas virtuais Linux em um laboratório de sala de aula de Serviços de Laboratório Azure
Este artigo mostra como os alunos podem ligar-se a uma máquina virtual Linux (VM) num laboratório utilizando RDP/SSH. 

Um instrutor precisa de ativar a funcionalidade de ligação remota de ambiente de trabalho antes que os alunos possam ligar-se ao VM do laboratório de sala de aula. Para obter instruções sobre como um instrutor pode ativar a função de ligação remota para desktop, consulte [Ativar o ambiente de trabalho remoto para máquinas virtuais Linux](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> Ativar a **ligação remota** de ambiente de trabalho apenas abre a porta **RDP** nas máquinas Linux. Um instrutor pode ligar-se à máquina Linux utilizando o SSH pela primeira vez e instalar pacotes RDP e GUI para que possa ligar-se à máquina Linux utilizando RDP mais tarde. 

## <a name="connect-to-the-student-vm"></a>Ligue-se ao estudante VM
Os alunos podem fazer RDP nos seus VMs Linux depois de o dono do laboratório (educador) **publicar** o modelo VM com pacotes RDP e GUI instalados na máquina. Eis os passos: 

1. Quando um aluno entra diretamente no portal labs ( ou através de uma ligação de `https://labs.azure.com` registo), é apresentado um azulejo para cada laboratório a que o aluno tem `https://labs.azure.com/register/<registrationCode>` acesso. 
2. No azulejo, altere o botão para iniciar o VM se estiver em estado de paragem. 
3. Selecione **Ligar**. Você vê duas opções para ligar ao VM: **SSH** e **Ambiente de Trabalho Remoto**.

    ![Student VM - opções de conexão](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Conecte-se utilizando SSH ou RDP
Se selecionar a opção **SSH,** consulte o seguinte Connect à sua caixa de diálogo **virtual:**  

![Cadeia de ligação SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selecione o botão **Copiar** ao lado da caixa de texto para copiá-lo para a área de recortes. Guarde a cadeia de ligação SSH. Utilize esta cadeia de ligação a partir de um terminal SSH (como [putty)](https://www.putty.org/)para ligar à máquina virtual.

Se selecionar a opção **RDP,** um ficheiro RDP é descarregado para a sua máquina. Guarde-o e abra-o para ligar à máquina. 

## <a name="next-steps"></a>Passos seguintes
Para aprender a ativar a função de ligação remota para os VMs linux num laboratório de sala de aula, consulte [O ambiente de trabalho remoto Enable para máquinas virtuais Linux](how-to-enable-remote-desktop-linux.md). 

