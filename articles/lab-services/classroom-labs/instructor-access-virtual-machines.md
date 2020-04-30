---
title: Instrutor que acede vMs em Serviços de Laboratório Azure
description: Este artigo mostra como os instrutores podem aceder aos seus VMs estudantis a partir da visão do instrutor. Por exemplo, um assistente de ensino pode ser instrutor para uma aula, mas um aluno para outras aulas.
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641943"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Aceda a máquinas virtuais como estudante a partir da vista do instrutor
Este artigo mostra como os instrutores podem aceder aos seus VMs para as aulas que frequentam como alunos. 

Aqui está um cenário onde esta funcionalidade vai ajudar. Um assistente de ensino é instrutor para uma aula, mas um aluno de outras aulas. E, o instrutor de ensino quer ver e aceder aos VMs dos alunos da visão do instrutor que mostra os laboratórios que possuem. 

## <a name="access-vms-from-instructor-view"></a>Acesso VMs a partir da vista do instrutor

1. Inscreva-se no site da [Azure Lab Services.](https://labs.azure.com) Vê os laboratórios que tem. Estes laboratórios podem ser laboratórios que criaste a ti ou aos laboratórios que lhe foram atribuídos como donos. Para mais informações, consulte [como adicionar proprietários adicionais a um laboratório existente](how-to-add-user-lab-owner.md)
2. Para aceder a VMs para aulas que frequenta enquanto estudante, selecione o ícone do computador no canto superior direito. Confirme que vê VMs a que pode aceder enquanto estudante. No exemplo seguinte, o utilizador é assistente docente do laboratório Python, mas um estudante do laboratório de Java. Então, o utilizador vê o VM do laboratório de Java na lista de entrega. O utilizador pode iniciar o VM e ligar-se ao mesmo. 
    
    ![Acesso a VMs de estudantes](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Ligar a uma VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Ligue-se a um VM usando RDP num Mac](connect-virtual-machine-mac-rdp.md)
- [Utilize o ambiente de trabalho remoto para máquinas virtuais Linux](how-to-use-remote-desktop-linux-student.md)
