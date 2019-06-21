---
title: Definir palavras-passe para as VMs no Azure Lab Services | Documentos da Microsoft
description: Saiba como definir e repor palavras-passe para as máquinas virtuais (VMs) em laboratórios de sala de aula do Azure Lab Services.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144114"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Definir ou repor a palavra-passe para as máquinas virtuais nos laboratórios de sala de aula
Este artigo fornece formas diferentes de definição e resettings palavras-passe para aceder a VMs nos laboratórios de sala de aula. 

## <a name="lab-owners-teachers"></a>Proprietários de laboratório (professores)
Proprietário de um laboratório (professor) pode conjunto/repor a palavra-passe para as VMs no momento de criar o laboratório (Assistente de criação de laboratório) ou depois de criar o laboratório (no dashboard). 

### <a name="set-password-at-the-time-of-lab-creation"></a>Definir a palavra-passe no momento da criação de laboratório
Proprietário de um laboratório (professor) pode definir uma palavra-passe para as VMs no laboratório sobre o **definir credenciais** página do Assistente de criação de laboratório.

![Definir credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)

Ao ativar/desativar a **utilizar a mesma palavra-passe para todas as máquinas virtuais** opção nesta página, um professor pode optar por utilizar a mesma palavra-passe para todas as VMs no laboratório ou que os alunos possam definir palavras-passe para as suas VMs. Por predefinição, esta definição está ativada para todos os Windows e Linux, imagens de sistema operativo, exceto o Ubuntu. Quando esta definição estiver desativada, os estudantes serão solicitados para definir uma palavra-passe quando o utilizador tentar ligar à VM pela primeira vez. 

O proprietário do laboratório pode repor esta palavra-passe (se necessário) sobre o **modelo de configurar** página do Assistente de criação de laboratório. 

![Configurar a página do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

O proprietário de laboratório também pode repor a palavra-passe depois de criar o laboratório, no dashboard. 

### <a name="reset-password-on-the-dashboard"></a>Repor palavra-passe no dashboard

1. Selecione o menu de capacidade excedida (três pontos verticais) no mosaico do laboratório e selecione **Repor palavra-passe**. 

    ![Reposição de palavra-passe menu na home page](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Sobre o **definir palavra-passe** caixa de diálogo, introduza uma palavra-passe e selecione **definir palavra-passe**.
    
    ![Caixa de diálogo Definir palavra-passe](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Utilizadores de laboratório (estudantes)
No momento de criar o laboratório, o proprietário de laboratório pode ativar ou desativar a **utilizar a mesma palavra-passe para todas as máquinas virtuais**. Se esta opção estiver ativada, os estudantes não é possível repor a palavra-passe. Todas as VMs nos laboratórios terá a mesma palavra-passe é definido pelo professor. 

Se esta opção estiver desativada, os utilizadores terão de definir uma palavra-passe quando tentar ligar à VM pela primeira vez. Quando os utilizadores (estudantes) selecionam a **Connect** botão no mosaico do laboratório a **minhas máquinas virtuais** página, o utilizador vê a caixa de diálogo seguinte para definir a palavra-passe para a VM: 

![Repor palavra-passe para o aluno](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

Estudante também pode definir a palavra-passe, clicando no menu de capacidade excedida (**reticências verticais**) no mosaico do laboratório e selecionando **Repor palavra-passe**. 

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre outras opções de utilização de estudante (como proprietário de um laboratório) pode configurar, consulte o seguinte artigo: [Configurar a utilização de estudante](how-to-configure-student-usage.md).
