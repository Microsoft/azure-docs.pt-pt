---
title: Educador que acede aos VMs nos Serviços do Laboratório Azure
description: Este artigo mostra como os educadores podem aceder aos seus VMs dos seus alunos a partir da visão educadora. Por exemplo, um assistente de ensino pode ser um educador para uma aula, mas um aluno para outras aulas.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: ac17f6853d8531a5854d161f41300f1bbec41552
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895767"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>Aceda a máquinas virtuais como estudante a partir da visão educadora
Este artigo mostra como os educadores podem aceder aos seus VMs para as aulas que frequentam enquanto alunos. 

Aqui está um cenário em que esta funcionalidade vai ajudar. Um assistente de ensino é um educador para uma aula, mas um aluno em outras turmas. E, o assistente docente quer ver e aceder aos VMs dos alunos a partir da visão educadora que mostra os laboratórios que possuem. 

## <a name="access-vms-from-educator-view"></a>Acesso VMs a partir da vista do educador

1. Inscreva-se no site da [Azure Lab Services](https://labs.azure.com). Vê os laboratórios que tem. Estes laboratórios podem ser laboratórios que criaste a ti próprio ou aos laboratórios que te atribuíram como dono. Para obter mais informações, consulte [Como adicionar proprietários adicionais a um laboratório existente](how-to-add-user-lab-owner.md)
2. Para aceder aos VMs para as aulas que frequenta como estudante, selecione o ícone do computador no canto superior direito. Confirme que vê VMs a que pode aceder como estudante. No exemplo seguinte, o utilizador é assistente de ensino para o laboratório Python, mas um estudante do laboratório de Java. Então, o utilizador vê o VM do laboratório java na lista de entrega. O utilizador pode iniciar o VM e ligá-lo. 
    
    ![Acesso vMs de estudante](./media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Ligar a uma VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Ligue-se a um VM usando RDP num Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Ligue-se a um VM usando RDP num Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)
- [Utilize ambientes de trabalho remotos para máquinas virtuais Linux](how-to-use-remote-desktop-linux-student.md)
