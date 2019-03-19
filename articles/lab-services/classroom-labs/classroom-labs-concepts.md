---
title: Conceitos de laboratórios de sala de aula - Azure Lab Services | Documentos da Microsoft
description: Conheça os conceitos básicos de serviços de laboratório e como ele pode facilitar criar e gerir laboratórios.
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
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 82f9d7090d7283e0b00b36e0928fffb3395ca4c0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102640"
---
# <a name="classroom-labs-concepts"></a>Conceitos dos Laboratórios de Sala de Aula
A lista seguinte contém os principais conceitos do Lab Services e definições:

## <a name="quota"></a>Quota
Quota é o limite de tempo (em horas), que pode definir um professor de estudante utilizar um laboratório de VM. Ele pode ser definido como 0, ilimitado, ou um número específico de horas. Se a quota é definida como 0, um estudante só pode utilizar a máquina virtual quando uma agenda está em execução ou quando um professor ativa manualmente a máquina virtual para o aluno.
 
## <a name="schedules"></a>Agendas
Agendas são os blocos de tempo (uma vez ou periodicamente) que um professor pode criar para a classe. Todas as máquinas virtuais no laboratório são iniciadas automaticamente no início para a agenda e são parados no final da agenda. Horas de quota não são utilizadas quando uma agenda está em execução.

## <a name="template-virtual-machine"></a>Máquina virtual de modelo
Uma máquina virtual do modelo num laboratório é uma imagem de máquina virtual base a partir da qual são criadas máquinas de virtuais de todos os utilizadores. Os criadores de Trainers/laboratório configurar a máquina virtual do modelo e configurá-lo com o software que pretende fornecer aos participantes de treinamento para fazer laboratórios. Quando publica um modelo de VM, o Azure Lab Services cria ou atualiza a VMs de laboratório com base no modelo de VM. 


## <a name="user-profiles"></a>Perfis de utilizador
Este artigo descreve perfis de utilizador diferentes no Azure Lab Services. 

### <a name="lab-account-owner"></a>Proprietário da conta de laboratório
Normalmente, o administrador de TI dos recursos de cloud da organização que é o proprietário da subscrição do Azure age como o proprietário da conta de laboratório e realiza as seguintes tarefas:   

- Configura uma conta de laboratório para a organização.
- Gere e configura políticas em todos os laboratórios.
- Dá permissões a pessoas na organização para criarem um laboratório na conta de laboratório.

### <a name="professor"></a>Professor
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

## <a name="next-steps"></a>Passos Seguintes
Introdução à configuração de uma conta de laboratório necessária para criar um laboratório de sala de aula com o Azure Lab Services:

- [Configurar uma conta de laboratório](tutorial-setup-lab-account.md)
