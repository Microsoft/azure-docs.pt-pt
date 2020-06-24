---
title: Como ligar-se a um VM de Serviços Azure Lab do Chromebook Microsoft Docs
description: Saiba como ligar-se de um Chromebook a uma máquina virtual nos Serviços Azure Lab.
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
ms.openlocfilehash: 5919eb4107ca234951597182017447c2d6ee1c1b
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897398"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Conecte-se a um VM utilizando o Protocolo de Ambiente de Trabalho Remoto num Chromebook
Esta secção mostra como um aluno pode ligar-se a um laboratório de sala de aula VM de um Chromebook usando RDP.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Instale o Microsoft Remote Desktop num Chromebook
1. Abra a App Store no seu Chromebook e procure o **Microsoft Remote Desktop**.

    ![Microsoft Remote Desktop](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
1. Instale a versão mais recente do Microsoft Remote Desktop. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Aceda ao VM a partir do seu Chromebook utilizando RDP
1. Abra o ficheiro **RDP** que é descarregado no seu computador com **o Microsoft Remote Desktop** instalado. Deve começar a ligar-se ao VM. 

    ![Ligar à VM](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Quando solicitado, insira a sua senha.
    ![Ligar à VM](./media/how-to-use-classroom-lab/password-chromebook.png)


1. **Selecione Continue** se receber o seguinte aviso. 

    ![Aviso de certificado](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Deve ver o ambiente de trabalho do VM a que está a ligar.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a ligação aos VMs Linux, consulte [as máquinas virtuais Connect to Linux](how-to-use-remote-desktop-linux-student.md)


