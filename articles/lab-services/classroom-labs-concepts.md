---
title: Conceitos de laboratório - Azure Lab Services | Microsoft Docs
description: Aprenda os conceitos básicos dos Serviços de Laboratório, e como pode facilitar a criação e gestão de laboratórios.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa3a8dad195b4b3cbf0786c8923c8b330d148898
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96435523"
---
# <a name="labs-concepts"></a>Conceitos dos laboratórios

A lista que se segue contém conceitos e definições de serviços de laboratório fundamentais:

## <a name="quota"></a>Quota

Quota é o prazo limite (em horas) que um educador pode definir para um aluno usar um VM de laboratório. Pode ser definido para 0, ou um número específico de horas. Se a quota estiver definida para 0, um aluno só pode utilizar a máquina virtual quando um horário está em funcionamento ou quando um educador liga manualmente a máquina virtual para o aluno.  

As horas de quota são contadas quando o aluno começa o laboratório vm.  Se um educador iniciar manualmente o VM do laboratório para um aluno, as horas de quota não são usadas para esse aluno.

## <a name="schedules"></a>Agendas

Os horários são os horários que um educador pode criar para a aula para que os VMs dos alunos estejam disponíveis para o horário de aula.  Os horários podem ser únicos ou recorrentes.  As horas de quota não são usadas quando um horário está a decorrer.

Existem três tipos de horários: Standard, Start only e Stop only.

- **Standard**.  Este horário iniciará todos os VMs dos alunos na hora de início especificada e desligará todos os VMs dos alunos no tempo de paragem especificado.
- **Comece apenas.**   Este horário iniciará todos os VMs dos alunos no momento especificado.  Os VMs dos estudantes não serão parados até que um aluno pare o seu VM através do portal Azure Lab Services ou ocorra um horário de paragem.
- **Pare apenas.**  Este horário irá parar todos os VMs dos alunos no momento especificado.  

## <a name="template-virtual-machine"></a>Máquina virtual de modelo

Uma máquina virtual de modelo em um laboratório é uma imagem de máquina virtual base a partir da qual todas as máquinas virtuais dos utilizadores são criadas. Os formadores/criadores de laboratório configuram a máquina virtual do modelo e configuram-na com o software que querem fornecer aos participantes do treino para fazerem laboratórios. Ao publicar um modelo VM, a Azure Lab Services cria ou atualiza VMs de laboratório com base no modelo VM.

## <a name="user-profiles"></a>Perfis de utilizador

Este artigo descreve perfis de utilizador diferentes no Azure Lab Services.

### <a name="lab-account-owner"></a>Proprietário da conta de laboratório

Tipicamente, um administrador de TI dos recursos em nuvem da organização, que detém a subscrição do Azure, atua como proprietário de uma conta de laboratório e faz as seguintes tarefas:

- Configura uma conta de laboratório para a organização.
- Gere e configura políticas em todos os laboratórios.
- Dá permissões a pessoas na organização para criarem um laboratório na conta de laboratório.

### <a name="educator"></a>Educador

Normalmente, utilizadores como um professor ou um treinador on-line criam laboratórios sob uma conta de laboratório. Um educador realiza as seguintes tarefas:

- Cria um laboratório de sala de aula.
- Cria máquinas virtuais no laboratório.
- Instala o software adequado em máquinas virtuais.
- Especifica quem pode aceder ao laboratório.
- Fornece a ligação de registo para o laboratório aos estudantes.

### <a name="student"></a>Estudante

O aluno realiza as seguintes tarefas:

- Utiliza a ligação de registo que o utilizador do laboratório recebe do criador do laboratório para se registar no laboratório.
- Liga-se a uma máquina virtual no laboratório e utiliza-a para tarefas, projetos ou para fazer trabalho da aula.

## <a name="next-steps"></a>Passos seguintes

Introdução à configuração de uma conta de laboratório necessária para criar um laboratório de sala de aula com o Azure Lab Services:

- [Configurar uma conta de laboratório](tutorial-setup-lab-account.md)
