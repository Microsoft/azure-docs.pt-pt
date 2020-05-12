---
title: Utilizar laboratórios de sala de aula para formações - Serviços de Laboratório Azure
description: Este artigo descreve como usar o Azure DevTest Labs para criar laboratórios em Azure para cenários de treino.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: 54fd8caef51a17148714d9cf90328572433c59db
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115120"
---
# <a name="use-classroom-labs-for-trainings"></a>Use laboratórios de sala de aula para treinos
A Azure Labs Services permite que professores, formadores ou assistentes de ensino criem de forma rápida e fácil um laboratório online para fornecer ambientes de aprendizagem pré-configurados para os estagiários. Cada estagiário seria capaz de utilizar ambientes idênticos e isolados para a formação. As políticas só podem ser aplicadas para garantir que os ambientes de formação só estejam disponíveis para cada estagiário quando necessitam e contenham recursos suficientes - como máquinas virtuais - necessários para a formação. 

![Laboratório de sala de aula](../media/classroom-labs-scenarios/classroom.png)

Os Laboratórios de Sala de Aula satisfazem os seguintes requisitos que são necessários para realizar formação em qualquer ambiente virtual: 

- Os estagiários podem fornecer rapidamente os seus ambientes de formação
- Todas as máquinas de treino devem ser idênticas.
- Estagiários não podem ver VMs criados por outros estagiários
- Custo de controlo garantindo que os estagiários não podem obter mais VMs do que precisam para a formação e também desligar VMs quando não estão a usá-los
- Partilhe facilmente o laboratório de formação com cada estagiário
- Reutilizar o laboratório de treino uma e outra vez

Neste artigo, você aprende sobre várias funcionalidades do Azure Lab Services que podem ser usadas para atender aos requisitos de formação previamente descritos e passos detalhados que você pode seguir para configurar um laboratório para o treino.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Crie a conta de laboratório como administrador de conta de laboratório
O primeiro passo na utilização dos Serviços De Laboratório Azure é criar uma conta de laboratório no portal Azure. Depois de um administrador de conta de laboratório criar a conta de laboratório, o administrador adiciona utilizadores que querem criar laboratórios para o papel de Criador de **Laboratório.** Os formadores criam laboratórios com máquinas virtuais para os alunos fazerem exercícios para o curso que estão a dar. Para mais detalhes, consulte Criar e gerir a conta de [laboratório.](how-to-manage-lab-accounts.md)

## <a name="create-and-manage-classroom-labs"></a>Criar e gerir laboratórios de sala de aula
Um treinador, que é membro do papel de Criador de Laboratório numa conta de laboratório, pode criar um ou mais laboratórios na conta do laboratório. Cria e configura um VM de modelo com todo o software necessário para fazer exercícios no seu curso. Escolhe-se uma imagem pronta a partir das imagens disponíveis para criar um laboratório de sala de aula e depois personalizá-la instalando o software necessário para o laboratório. Para mais detalhes, consulte [Criar e gerir laboratórios de sala de aula.](how-to-manage-classroom-labs.md)

## <a name="configure-usage-settings-and-policies"></a>Configurar as definições e políticas de utilização
O criador do laboratório pode adicionar ou remover os utilizadores para o laboratório, obter link de registo para enviar aos utilizadores do laboratório, configurar políticas como definir quotas individuais por utilizador, atualizar o número de VMs disponíveis no laboratório, e muito mais. Para mais detalhes, consulte [configurar as definições e políticas](how-to-configure-student-usage.md)de utilização .

## <a name="create-and-manage-schedules"></a>Criar e gerir agendamentos
Os horários permitem configurar um laboratório de sala de aula de modo a que os VMs no laboratório comecem e desliguem automaticamente num determinado momento. Pode definir um horário único ou um horário recorrente. Para mais detalhes, consulte [Criar e gerir horários para laboratórios de sala de aula.](how-to-create-schedules.md)

## <a name="set-up-and-publish-a-template-vm"></a>Configurar e publicar um modelo VM
Um modelo num laboratório é a imagem de máquina virtual de base da qual todas as máquinas virtuais dos utilizadores são criadas. Configure o modelo VM de modo a que esteja configurado com exatamente o que pretende fornecer aos participantes de formação. Pode fornecer um nome e descrição do modelo que os utilizadores do laboratório vão ver. Em seguida, publica o modelo para disponibilizar casos do modelo VM aos utilizadores do laboratório. Ao publicar um modelo, o Azure Lab Services cria VMs no laboratório através do modelo. O número de VMs criadas neste processo é igual ao número máximo de utilizadores permitidos no laboratório, o que pode configurar na política de utilização do laboratório. Todas as máquinas virtuais têm a mesma configuração do modelo. Para mais detalhes, consulte [Configurar e publicar máquinas virtuais de modelo](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Use VMs no laboratório de sala de aula
Um aluno ou participante de formação registra-se no laboratório, e conecta-se ao VM para fazer exercícios para o curso. Para mais detalhes, consulte [Como aceder a um laboratório de sala de aula.](how-to-use-classroom-lab.md)

## <a name="next-steps"></a>Passos seguintes
Comece com a criação de uma conta de laboratório em Laboratórios de Sala de Aula seguindo instruções no artigo: [Tutorial: Configurar uma conta](tutorial-setup-lab-account.md)de laboratório com os Serviços de Laboratório Azure .