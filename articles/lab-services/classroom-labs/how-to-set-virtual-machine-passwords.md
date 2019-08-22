---
title: Definir senhas para VMs no Azure Lab Services | Microsoft Docs
description: Saiba como definir e redefinir senhas para VMs (máquinas virtuais) em laboratórios de sala de aula do Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645016"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>Definir ou redefinir a senha para máquinas virtuais em laboratórios de sala de aula (instrutor)
Um professor (proprietário do laboratório) pode definir/redefinir a senha para VMs no momento da criação do laboratório (Assistente de criação de laboratório) ou depois de criar o laboratório (no painel). 

## <a name="set-password-at-the-time-of-lab-creation"></a>Definir a senha no momento da criação do laboratório
Um professor (proprietário do laboratório) pode definir uma senha para VMs no laboratório na página **definir credenciais** do assistente de criação de laboratório.

![Definir credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)

Ao habilitar/desabilitar a opção **usar a mesma senha para todas as máquinas virtuais** nesta página, um professor pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para suas VMs. Por padrão, essa configuração é habilitada para todas as imagens do sistema operacional Windows e Linux, exceto o Ubuntu. Quando essa configuração estiver desabilitada, os alunos serão solicitados a definir uma senha quando tentarem se conectar à VM pela primeira vez. 

O proprietário do laboratório pode redefinir essa senha (se necessário) na página **Configurar modelo** do assistente de criação de laboratório. 

![Configurar a página do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

O proprietário do laboratório também pode redefinir a senha após a criação do laboratório, no painel. 

## <a name="reset-password-on-the-dashboard"></a>Redefinir a senha no painel

1. Selecione o menu de estouro (três pontos verticais) no bloco do laboratório e selecione **Redefinir senha**. 

    ![Menu Redefinir senha na home page](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Na caixa de diálogo **Definir senha** , insira uma senha e selecione **Definir senha**.
    
    ![Caixa de diálogo Definir senha](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as outras opções de uso de aluno que você (como proprietário do laboratório) podem configurar, consulte o seguinte artigo: [Configure o uso do aluno](how-to-configure-student-usage.md).

Para saber mais sobre como os alunos podem redefinir senhas para suas VMs, consulte [definir ou Redefinir senha para máquinas virtuais em laboratórios de sala de aula (estudantes)](how-to-set-virtual-machine-passwords-student.md).