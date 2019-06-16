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
ms.openlocfilehash: 3701c69ff97d840f6cba175df8f02bc55ece498b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123209"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Definir ou repor a palavra-passe para as máquinas virtuais nos laboratórios de sala de aula
Este artigo fornece formas diferentes de definição e resettings palavras-passe para aceder a VMs nos laboratórios de sala de aula. 

## <a name="lab-owners-teachers"></a>Proprietários de laboratório (professores)
Proprietário de um laboratório (professor) pode definir uma palavra-passe para as VMs no laboratório sobre o **definir credenciais** página do Assistente de criação de laboratório.

![Definir credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)

Em seguida, o proprietário de laboratório pode repor a palavra-passe (se necessário) no **modelo de configurar** página do Assistente de criação de laboratório. 

![Configurar a página do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

O proprietário de laboratório também pode repor a palavra-passe depois de criar o laboratório, no dashboard. 

![Reposição de palavra-passe menu na home page](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)

Em seguida, introduza a nova palavra-passe no **definir palavra-passe** página e selecione **definir palavra-passe**. 

![Reposição de palavra-passe menu na home page](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Utilizadores de laboratório (estudantes)
No momento de criar o laboratório. Ao mesmo tempo, o proprietário de laboratório pode ativar ou desativar os estudantes que permite definir a palavra-passe para as VMs. Se o proprietário de laboratório (professor) ative esta opção, o utilizador de laboratório (para estudantes) terá uma opção para definir a palavra-passe para a VM quando o aluno inicia sessão VM pela primeira vez. Num **Windows VM**, prima **CTRL + ALT + END**e selecione **alterar palavra-passe**. 

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre outras opções de utilização de estudante (como proprietário de um laboratório) pode configurar, consulte o seguinte artigo: [Configurar a utilização de estudante](how-to-configure-student-usage.md).
