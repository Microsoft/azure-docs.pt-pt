---
title: Aceder a um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Neste tutorial, irá aceder a máquinas virtuais num laboratório de sala de aula configurado por um professor.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 5482ea720ea8d21230587dd9216bd006bf4e5a6e
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650653"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Tutorial: Aceder a um laboratório de sala de aula no Azure Lab Services
Neste tutorial, como estudante vai ligar-se a uma máquina virtual (VM) num laboratório de sala de aula. 

Neste tutorial, vai realizar as seguintes ações:

> [!div class="checklist"]
> * Utilizar a ligação de registo 
> * Ligar à máquina virtual

## <a name="use-the-registration-link"></a>Utilizar a ligação de registo

1. Navegue para o **URL de registo** que recebeu do professor/educador. Não precisa de utilizar o URL de registo depois de concluir o registo. Em alternativa, utilize o URL: [ https://labs.azure.com ](https://labs.azure.com). 
1. Inicie sessão no serviço com a sua conta escolar para concluir o registo. 
2. Depois de se registar, confirme se vê a máquina virtual do laboratório a que tem acesso. 
3. Aguarde até que a máquina virtual esteja pronta e, depois, **inicie-a**. Este processo demora algum tempo.  

    ![Iniciar a VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

1. Selecione **Connect** no mosaico para a máquina virtual do laboratório que pretende aceder. 

    ![Ligar à VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Efetue um dos seguintes passos: 
    1. Para **Windows** máquinas virtuais, guarde o **RDP** ficheiro para o disco rígido. Abra o ficheiro RDP para ligar à máquina virtual. Utilize o **nome de utilizador** e **palavra-passe** a partir do seu professor/professora para iniciar sessão máquina. 
    3. Para **Linux** máquinas virtuais, pode utilizar **SSH** ou **RDP** (se estiver ativada) para se ligar aos mesmos. Para obter mais informações, consulte [ativar a ligação de ambiente de trabalho remoto para máquinas Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, acedeu a um laboratório de sala de aulas através da ligação de registo que recebeu do seu professor/educador.

Como proprietário de um laboratório, queira ver quem tiver registrado com o seu laboratório e controlar a utilização de VMs. Avance para o próximo tutorial para saber como controlar a utilização do laboratório:

> [!div class="nextstepaction"]
> [Track usage of a lab](tutorial-track-usage.md) (Acompanhar a utilização de um laboratório) 
