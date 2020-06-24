---
title: Como ligar-se a um VM de Serviços Azure Lab da Mac Microsoft Docs
description: Saiba como ligar-se de um Mac a uma máquina virtual nos Serviços de Laboratório Azure.
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
ms.openlocfilehash: 091acbab20723ec33d52085a717d23adf261254e
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897384"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Ligue-se a um VM utilizando o Protocolo de Ambiente de Trabalho Remoto num Mac
Esta secção mostra como um aluno pode ligar-se a um laboratório de sala de aula VM de um Mac usando RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Instale o Microsoft Remote Desktop num Mac
1. Abra a App Store no seu Mac e procure o **Microsoft Remote Desktop**.

    ![Microsoft Remote Desktop](./media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Instale a versão mais recente do Microsoft Remote Desktop. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Aceda ao VM a partir do seu Mac utilizando RDP
1. Abra o ficheiro **RDP** que é descarregado no seu computador com **o Microsoft Remote Desktop** instalado. Deve começar a ligar-se ao VM. 

    ![Ligar à VM](./media/how-to-use-classroom-lab/connect-linux-vm.png)
1. **Selecione Continue** se receber o seguinte aviso. 

    ![Aviso de certificado](./media/how-to-use-classroom-lab/certificate-error.png)
1. Devia ver o VM. 

    > [!NOTE]
    > O exemplo a seguir é para um CentOS Linux VM. 

    ![VM](./media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Passos seguintes
Para aprender a ligar-se aos VMs Do Linux utilizando RDP, consulte [utilizar o ambiente de trabalho remoto para máquinas virtuais Linux](how-to-use-remote-desktop-linux-student.md)


