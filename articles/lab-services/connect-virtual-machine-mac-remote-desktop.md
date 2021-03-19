---
title: Como ligar-se a um VM de Serviços de Laboratório Azure da Mac | Microsoft Docs
description: Saiba como ligar-se de um Mac a uma máquina virtual nos Serviços de Laboratório Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 674bea13093e185ae991148c2cf96599a273d0bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85444629"
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


