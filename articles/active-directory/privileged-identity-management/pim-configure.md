---
title: O que é Privileged Identity Management? -Azure AD | Microsoft Docs
description: Fornece uma visão geral de Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578f45a7bc870874319a8238c7c8c50bf7a37998
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023030"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>O que é o Azure AD Privileged Identity Management?

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) é um serviço que permite gerenciar, controlar e monitorar o acesso a recursos importantes em sua organização. Esses recursos incluem recursos no Azure AD, Azure e outros serviços online da Microsoft, como o Office 365 ou Microsoft Intune.

## <a name="reasons-to-use"></a>Motivos para usar

As organizações desejam minimizar o número de pessoas que têm acesso a informações ou recursos seguros, pois isso reduz a chance de um ator mal-intencionado obter esse acesso ou um usuário autorizado que afete inadvertidamente um recurso confidencial. No entanto, os utilizadores continuam a ter de realizar operações privilegiadas em aplicações do Azure AD, Azure, Office 365 ou SaaS. As organizações podem fornecer ao usuário acesso privilegiado JIT (just-in-time) aos recursos do Azure e ao Azure AD. Há uma necessidade de supervisão para o que esses usuários estão fazendo com seus privilégios de administrador.

## <a name="what-does-it-do"></a>Para que serve?

O Privileged Identity Management fornece ativação de função baseada em tempo e aprovação para atenuar os riscos de permissões de acesso excessivas, desnecessárias ou de uso inoportuno em recursos dos quais você se preocupa. Aqui estão alguns dos principais recursos do Privileged Identity Management:

- Fornecer acesso privilegiado **just-in-time** ao Azure AD e aos recursos do Azure
- Atribuir acesso com **limite de tempo** a recursos usando datas de início e término
- Exigir **aprovação** para ativar funções com privilégios
- Impor a **autenticação multifator** para ativar qualquer função
- Use a **justificação** para entender por que os usuários ativam
- Obter **notificações** quando funções com privilégios forem ativadas
- Realizar **revisões de acesso** para garantir que os usuários ainda precisem de funções
- Baixar o **histórico de auditoria** para auditoria interna ou externa

## <a name="what-can-i-do-with-it"></a>O que posso fazer com ele?

Depois de configurar Privileged Identity Management, você verá as opções **tarefas**, **gerenciar**e **atividade** no menu de navegação à esquerda. Como administrador, você escolherá entre gerenciar **funções do Azure ad** e funções de **recursos do Azure** . Ao escolher o tipo de funções a serem gerenciadas, você verá um conjunto semelhante de opções para esse tipo de função.

![Captura de tela de Privileged Identity Management no portal do Azure](./media/pim-configure/pim-overview.png)

## <a name="who-can-do-what"></a>Quem pode fazer o quê?

Se você for a primeira pessoa a usar Privileged Identity Management, receberá automaticamente as funções [administrador de segurança](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) no diretório.

Para funções do Azure AD no Privileged Identity Management, somente um usuário que está na função de administrador de função com privilégios pode gerenciar atribuições para outros administradores. Você pode [conceder acesso a outros administradores para gerenciar Privileged Identity Management](pim-how-to-give-access-to-pim.md). Os administradores globais, os administradores de segurança, os leitores globais e os leitores de segurança também podem exibir as atribuições às funções do Azure AD no Privileged Identity Management.

Para funções de recurso do Azure no Privileged Identity Management, somente um administrador de assinatura, um proprietário de recurso ou um administrador de acesso de usuário de recurso pode gerenciar atribuições para outros administradores. Os usuários que são administradores de função com privilégios, administradores de segurança ou leitores de segurança, por padrão, não têm acesso para exibir atribuições às funções de recurso do Azure no Privileged Identity Management.

## <a name="scenarios"></a>Cenários

O Privileged Identity Management suporta os seguintes cenários:

### <a name="privileged-role-administrator-permissions"></a>Permissões de administrador de função com privilégios

- Ativar a aprovação de funções específicas
- Especificar usuários ou grupos aprovadores para aprovar solicitações
- Ver o histórico de pedidos e de aprovação de todas as funções com privilégios

### <a name="approver-permissions"></a>Permissões do aprovador

- Ver aprovações pendentes (pedidos)
- Aprovar ou rejeitar solicitações de elevação de função (única e em massa)
- Fornecer justificativa para minha aprovação ou rejeição

### <a name="eligible-role-user-permissions"></a>Permissões de usuário de função qualificada

- Pedir a ativação de uma função que requer aprovação
- Ver o estado do seu pedido para ativação
- Completar a sua tarefa no Azure AD se a ativação for aprovada

## <a name="terminology"></a>Terminologia

Para entender melhor Privileged Identity Management e sua documentação, você deve examinar os termos a seguir.

| Termo ou conceito | Categoria de atribuição de função | Descrição |
| --- | --- | --- |
| direito | Tipo | Uma atribuição de função que exige que um usuário execute uma ou mais ações para usar a função. Se um usuário se tornou qualificado para uma função, isso significa que eles podem ativar a função quando precisam executar tarefas privilegiadas. Não há nenhuma diferença no acesso dado a alguém com uma atribuição de funções qualificada e permanente. A única diferença é que algumas pessoas não precisam desse acesso o tempo todo. |
| ativo | Tipo | Uma atribuição de função que não exige que um usuário execute qualquer ação para usar a função. Os usuários atribuídos como ativos têm os privilégios atribuídos à função. |
| ativar |  | O processo de executar uma ou mais ações para usar uma função à qual um usuário está qualificado. As ações podem incluir a execução de uma verificação de MFA (autenticação multifator), o fornecimento de uma justificativa de negócios ou a solicitação de aprovação de aprovadores designados. |
| associada | Estado | Um usuário que tem uma atribuição de função ativa. |
| ativado | Estado | Um usuário que tem uma atribuição de função qualificada, executou as ações para ativar a função e agora está ativo.  Depois de ativado, o usuário pode usar a função para um período de tempo pré-configurado antes de precisar ativá-lo novamente. |
| qualificado permanente | Duração | Uma atribuição de função em que um usuário sempre está qualificado para ativar a função. |
| ativo permanente | Duração | Uma atribuição de função em que um usuário sempre pode usar a função sem executar nenhuma ação. |
| expiração qualificada | Duração | Uma atribuição de função em que um usuário está qualificado para ativar a função em uma data de início e de término especificada. |
| expirar ativo | Duração | Uma atribuição de função em que um usuário pode usar a função sem executar nenhuma ação em uma data de início e de término especificada. |
| acesso just-in-time (JIT) |  | Um modelo no qual os usuários recebem permissões temporárias para executar tarefas privilegiadas, o que impede que usuários mal-intencionados ou não autorizados obtenham acesso depois que as permissões tiverem expirado. O acesso é concedido somente quando os usuários precisam dele. |
| princípio de acesso de privilégios mínimos |  | Uma prática de segurança recomendada na qual cada usuário é fornecido apenas com os privilégios mínimos necessários para realizar as tarefas que eles estão autorizados a executar. Essa prática minimiza o número de administradores globais e, em vez disso, usa funções de administrador específicas para determinados cenários. |

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Para obter informações sobre licenças para usuários, consulte [requisitos de licença para usar Privileged Identity Management](subscription-requirements.md).

## <a name="next-steps"></a>Passos seguintes

- [Requisitos de licença para usar o Privileged Identity Management](subscription-requirements.md)
- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implantar Privileged Identity Management](pim-deployment-plan.md)
