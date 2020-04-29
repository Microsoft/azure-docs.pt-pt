---
title: Como ligar a um VM azure lab services de Mac [ Microsoft Docs
description: Este artigo
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503091"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Ligue-se a um VM usando RDP num Mac
Esta secção mostra como um aluno pode ligar-se a um vM de laboratório de sala de aula a partir de um Mac usando RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Instale o Ambiente de Trabalho Remoto da Microsoft num Mac
1. Abra a App Store no seu Mac e procure por **Microsoft Remote Desktop**.

    ![Microsoft Remote Desktop](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Instale a versão mais recente do Microsoft Remote Desktop. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Aceda ao VM do seu Mac utilizando RDP
1. Abra o ficheiro **RDP** que é descarregado no seu computador com o **Microsoft Remote Desktop** instalado. Deve começar a ligar-se ao VM. 

    ![Ligar à VM](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Selecione **Continue** se receber o seguinte aviso. 

    ![Aviso de certificado](../media/how-to-use-classroom-lab/certificate-error.png)
1. Devia ver o VM. 

    > [!NOTE]
    > O exemplo que se segue é para um CentOS Linux VM. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Passos seguintes
Para aprender a ligar-se aos VMs Linux utilizando RDP, consulte [Use desktop remoto para máquinas virtuais Linux](how-to-use-remote-desktop-linux-student.md)


