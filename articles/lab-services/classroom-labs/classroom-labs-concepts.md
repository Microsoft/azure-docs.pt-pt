---
title: Conceitos de Laboratórios de Sala de Aula - Serviços de Laboratório Azure / Microsoft Docs
description: Aprenda os conceitos básicos dos Serviços laboratoriais, e como pode facilitar a criação e gestão de laboratórios.
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
ms.openlocfilehash: 548cacfb76aba9093a59a5c87525d038558bf353
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592632"
---
# <a name="classroom-labs-concepts"></a>Conceitos dos Laboratórios de Sala de Aula

A lista seguinte contém conceitos e definições chave lab Services:

## <a name="quota"></a>Quota

A quota é o limite de tempo (em horas) que um educador pode definir para um aluno usar um VM de laboratório. Pode ser definido para 0, ou um número específico de horas. Se a quota estiver definida para 0, um aluno só pode utilizar a máquina virtual quando um horário está em execução ou quando um educador liga manualmente a máquina virtual para o aluno.  

As horas de quota são contadas quando o aluno começa o laboratório vM em si.  Se um educador iniciar manualmente o VM de laboratório para um aluno, as horas de quota não são usadas para esse aluno.

## <a name="schedules"></a>Agendas

Os horários são as faixas horárias que um educador pode criar para a aula para que os VMs dos alunos estejam disponíveis para a hora das aulas.  Os horários podem ser únicos ou recorrentes.  As horas de quota não são usadas quando um horário está a decorrer.

Existem três tipos de horários: Standard, Start only e Stop only.

- **Padrão.**  Este horário iniciará todos os VMs dos estudantes na hora de início especificada e encerrará todos os VMs dos estudantes no tempo de paragem especificado.
- **Começar apenas.**   Esta programação iniciará todos os VMs dos estudantes no momento especificado.  Os VMs dos estudantes não serão parados até que um aluno pare o seu VM através do portal Azure Lab Services ou ocorra apenas um horário de paragem.
- **Pare apenas.**  Este horário irá parar todos os VMs dos estudantes no momento especificado.  

## <a name="template-virtual-machine"></a>Máquina virtual de modelo

Uma máquina virtual modelo em um laboratório é uma imagem de máquina virtual base a partir da qual todas as máquinas virtuais dos utilizadores são criadas. Os formadores/criadores de laboratório configuram o modelo de máquina virtual e configuram-na com o software que querem fornecer aos participantes para treinar os participantes para fazer em laboratórios. Quando publica um modelo VM, o Azure Lab Services cria ou atualiza vMs de laboratório com base no modelo VM.

## <a name="user-profiles"></a>Perfis de utilizador

Este artigo descreve perfis de utilizador diferentes no Azure Lab Services.

### <a name="lab-account-owner"></a>Proprietário da conta de laboratório

Tipicamente, um administrador de TI dos recursos cloud da organização, que detém a subscrição do Azure, atua como proprietário de uma conta de laboratório e faz as seguintes tarefas:

- Configura uma conta de laboratório para a organização.
- Gere e configura políticas em todos os laboratórios.
- Dá permissões a pessoas na organização para criarem um laboratório na conta de laboratório.

### <a name="educator"></a>Educador

Normalmente, os utilizadores como um professor ou um formador online criam laboratórios de sala de aula numa conta de laboratório. Um educador realiza as seguintes tarefas:

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
