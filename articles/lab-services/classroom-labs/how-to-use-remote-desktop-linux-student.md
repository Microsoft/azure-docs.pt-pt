---
title: Usar a área de trabalho remota para Linux no Azure Lab Services | Microsoft Docs
description: Saiba como usar a área de trabalho remota para máquinas virtuais do Linux em um laboratório no Azure Lab Services.
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
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585080"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Use a área de trabalho remota para máquinas virtuais Linux em um laboratório de sala de aula de Azure Lab Services
Este artigo mostra como os alunos podem se conectar a uma VM (máquina virtual) do Linux em um laboratório usando RDP/SSH. 

Um instrutor precisa habilitar o recurso de conexão de área de trabalho remota antes que os alunos possam se conectar à VM do laboratório da sala de aula. Para obter instruções sobre como um instrutor pode habilitar o recurso de conexão de área de trabalho remota, consulte [habilitar a área de trabalho remota para máquinas virtuais Linux](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> Habilitar a **conexão de área de trabalho remota** abre apenas a porta **RDP** em computadores Linux. Um instrutor pode se conectar ao computador Linux usando SSH pela primeira vez e instalar pacotes RDP e GUI para que você possa se conectar ao computador Linux usando o RDP mais tarde. 

## <a name="connect-to-the-student-vm"></a>Conectar-se à VM do aluno
Os alunos podem RDP em suas VMs Linux depois que o proprietário do laboratório (professor/professor) **publica** a VM do modelo com pacotes RDP e GUI instalados no computador. Eis os passos: 

1. Quando um aluno entra no portal de laboratórios diretamente (`https://labs.azure.com`) ou usando um link de registro (`https://labs.azure.com/register/<registrationCode>`), um bloco para cada laboratório ao qual o aluno tem acesso é exibido. 
2. No bloco, alterne o botão para iniciar a VM se ela estiver no estado parado. 
3. Selecione **Ligar**. Você verá duas opções para se conectar à VM: **SSH** e **área de trabalho remota**.

    ![VM do aluno-opções de conexão](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Conectar-se usando SSH ou RDP
Se você selecionar a opção **SSH** , você verá a seguinte caixa de diálogo **conectar-se à sua máquina virtual** :  

![Cadeia de conexão SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selecione o botão **copiar** ao lado da caixa de texto para copiá-la para a área de transferência. Salve a cadeia de conexão SSH. Use essa cadeia de conexão de um terminal SSH ( [como uma](https://www.putty.org/)reseqüência) para se conectar à máquina virtual.

Se você selecionar a opção **RDP** , um arquivo RDP será baixado em seu computador. Salve-o e abra-o para se conectar ao computador. 

## <a name="next-steps"></a>Passos seguintes
Para saber como habilitar o recurso de conexão de área de trabalho remota para VMs do Linux em um laboratório de sala de aula, consulte [habilitar área de trabalho remota para máquinas virtuais Linux](how-to-enable-remote-desktop-linux.md). 

