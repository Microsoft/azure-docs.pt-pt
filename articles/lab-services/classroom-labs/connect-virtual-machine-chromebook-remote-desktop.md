---
title: Como ligar a um VM de Serviços de Laboratório Azure do Chromebook [ Microsoft Docs
description: Saiba como ligar-se de um Chromebook a uma máquina virtual nos Serviços de Laboratório Azure.
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
ms.date: 05/11/2020
ms.author: nicolela
ms.openlocfilehash: 581ba32f536728fec88ddf3120637f9347e2e925
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704472"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Ligue-se a um VM usando o Protocolo de Ambiente de Trabalho Remoto num Chromebook
Esta secção mostra como um aluno pode ligar-se a um VM de laboratório de sala de aula a partir de um Chromebook usando RDP.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Instale o Ambiente de Trabalho Remoto da Microsoft num Chromebook
1. Abra a App Store no seu Chromebook e procure por **Microsoft Remote Desktop**.

    ![Microsoft Remote Desktop](../media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
1. Instale a versão mais recente do Microsoft Remote Desktop. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Aceda ao VM a partir do seu Chromebook utilizando RDP
1. Abra o ficheiro **RDP** que é descarregado no seu computador com o **Microsoft Remote Desktop** instalado. Deve começar a ligar-se ao VM. 

    ![Ligar à VM](../media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Quando solicitado, introduza a sua senha.
    ![Ligar à VM](../media/how-to-use-classroom-lab/password-chromebook.png)


1. Selecione **Continue** se receber o seguinte aviso. 

    ![Aviso de certificado](../media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Deve ver o ambiente de trabalho do VM a que está a ligar.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a ligação aos VMs Linux, consulte [Connect to Linux virtual machines](how-to-use-remote-desktop-linux-student.md)


