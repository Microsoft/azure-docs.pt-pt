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
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 255cbff94e954dd05acfe77b97929c313b1dd3cf
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808581"
---
# <a name="classroom-labs-concepts"></a>Conceitos de laboratórios de sala de aula
A lista seguinte contém os principais conceitos do Lab Services e definições:

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
