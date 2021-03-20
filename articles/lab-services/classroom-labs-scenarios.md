---
title: Utilizar laboratórios para formações - Azure Lab Services
description: Este artigo descreve como usar a Azure DevTest Labs para criar laboratórios em Azure para cenários de treino.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f9eb3d44d470a978e676f6268cd693b283dd2703
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433566"
---
# <a name="use-labs-for-trainings"></a>Use laboratórios para treinos
A Azure Labs Services permite que educadores (professores, professores, formadores ou assistentes de ensino, etc.) criem de forma rápida e fácil um laboratório online para a disponibilização de ambientes de aprendizagem pré-configurados para os formandos. Cada estagiário poderia utilizar ambientes idênticos e isolados para a formação. As políticas podem ser aplicadas para garantir que os ambientes de formação só estejam disponíveis para cada estagiário quando estes necessitem e contenham recursos suficientes - como as máquinas virtuais - necessários para a formação. 

![Laboratório de sala de aula](./media/classroom-labs-scenarios/classroom.png)

os laboratórios cumprem os seguintes requisitos que são necessários para realizar formação em qualquer ambiente virtual: 

- Os estagiários podem rapidamente providenciar os seus ambientes de formação
- Todas as máquinas de treino devem ser idênticas.
- Estagiários não podem ver VMs criados por outros estagiários
- Custo de controlo, garantindo que os estagiários não podem obter mais VMs do que precisam para a formação e também desligar os VMs quando não estão a usá-los
- Partilhe facilmente o laboratório de treino com cada estagiário
- Reutilizar o laboratório de treino uma e outra vez

Neste artigo, você aprende sobre várias funcionalidades dos Azure Lab Services que podem ser usadas para satisfazer os requisitos de formação previamente descritos e passos detalhados que você pode seguir para configurar um laboratório para formação.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Criar a conta de laboratório como administrador de conta de laboratório
O primeiro passo na utilização dos Serviços Azure Lab é criar uma conta de laboratório no portal Azure. Depois de um administrador de conta de laboratório criar a conta de laboratório, o administrador adiciona utilizadores que querem criar laboratórios para o papel **de Criador de Laboratório.** Os educadores criam laboratórios com máquinas virtuais para os alunos fazerem exercícios para o curso que estão a ensinar. Para mais detalhes, consulte [Criar e gerir a conta de laboratório.](how-to-manage-lab-accounts.md)

## <a name="create-and-manage-labs"></a>Criar e gerir laboratórios
Um educador, que é membro do papel de Criador de Laboratório numa conta de laboratório, pode criar um ou mais laboratórios na conta do laboratório. Você cria e configura um VM modelo com todo o software necessário para fazer exercícios no seu curso. Você escolhe uma imagem pronta a partir das imagens disponíveis para criar um laboratório de sala de aula e depois personaliza-a instalando o software necessário para o laboratório. Para mais detalhes, consulte [Criar e gerir laboratórios.](how-to-manage-classroom-labs.md)

## <a name="configure-usage-settings-and-policies"></a>Configurar configurações e políticas de utilização
O criador do laboratório pode adicionar ou remover utilizadores ao laboratório, obter o link de registo para enviar aos utilizadores de laboratório, definir políticas como definir quotas individuais por utilizador, atualizar o número de VMs disponíveis no laboratório, e muito mais. Para mais informações, consulte [configurar as definições e políticas de utilização](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Criar e gerir agendamentos
Os horários permitem-lhe configurar um laboratório de sala de aula de tal forma que os VMs no laboratório comecem e desliguem automaticamente numa hora especificada. Pode definir um horário único ou um horário recorrente. Para mais detalhes, consulte [Criar e gerir horários para laboratórios.](how-to-create-schedules.md)

## <a name="set-up-and-publish-a-template-vm"></a>Configurar e publicar um modelo VM
Um modelo num laboratório é a imagem de máquina virtual de base da qual todas as máquinas virtuais dos utilizadores são criadas. Configurar o modelo VM de modo a que seja configurado com exatamente o que você quer fornecer aos participantes do treino. Pode fornecer um nome e descrição do modelo que os utilizadores do laboratório vão ver. Em seguida, publica o modelo para disponibilizar os casos do modelo VM aos utilizadores do laboratório. Ao publicar um modelo, o Azure Lab Services cria VMs no laboratório através do modelo. O número de VMs criadas neste processo é igual ao número máximo de utilizadores permitidos no laboratório, o que pode configurar na política de utilização do laboratório. Todas as máquinas virtuais têm a mesma configuração do modelo. Para mais detalhes, consulte [Configurar e publicar máquinas virtuais de modelo](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Use VMs no laboratório da sala de aula
Um aluno ou participante de formação registra-se no laboratório, e conecta-se ao VM para fazer exercícios para o curso. Para mais detalhes, consulte [Como aceder a um laboratório de sala de aula.](how-to-use-classroom-lab.md)

## <a name="next-steps"></a>Passos seguintes
Comece por criar uma conta de laboratório em laboratório seguindo as instruções do artigo: [Tutorial: Configurar uma conta de laboratório com os Serviços Azure Lab](tutorial-setup-lab-account.md).