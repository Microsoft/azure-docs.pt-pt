---
title: Educador aaceder a VMs em Serviços de Laboratório Azure
description: Este artigo mostra como os educadores podem aceder aos seus VMs estudantis a partir da visão do educador. Por exemplo, um assistente docente pode ser um educador para uma aula, mas um aluno para outras aulas.
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
ms.openlocfilehash: 449ff8aafd6dec1e9c0ff16dc407155949fd1313
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586172"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>Aceda a máquinas virtuais como estudante a partir da vista do educador
Este artigo mostra como os educadores podem aceder aos seus VMs para as aulas que frequentam enquanto estudantes. 

Aqui está um cenário onde esta funcionalidade vai ajudar. Uma assistente de ensino é educadora para uma aula, mas um aluno de outras turmas. E, a assistente de ensino quer ver e aceder aos VMs dos alunos da visão do educador que mostra os laboratórios que possuem. 

## <a name="access-vms-from-educator-view"></a>Acesso VMs da visão do educador

1. Inscreva-se no site da [Azure Lab Services.](https://labs.azure.com) Vê os laboratórios que tem. Estes laboratórios podem ser laboratórios que criaste a ti ou aos laboratórios que lhe foram atribuídos como donos. Para mais informações, consulte [como adicionar proprietários adicionais a um laboratório existente](how-to-add-user-lab-owner.md)
2. Para aceder a VMs para aulas que frequenta enquanto estudante, selecione o ícone do computador no canto superior direito. Confirme que vê VMs a que pode aceder enquanto estudante. No exemplo seguinte, o utilizador é assistente docente do laboratório Python, mas um estudante do laboratório de Java. Então, o utilizador vê o VM do laboratório de Java na lista de entrega. O utilizador pode iniciar o VM e ligar-se ao mesmo. 
    
    ![Acesso a VMs de estudantes](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Ligar a uma VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Ligue-se a um VM usando RDP num Mac](connect-virtual-machine-mac-rdp.md)
- [Utilize o ambiente de trabalho remoto para máquinas virtuais Linux](how-to-use-remote-desktop-linux-student.md)
