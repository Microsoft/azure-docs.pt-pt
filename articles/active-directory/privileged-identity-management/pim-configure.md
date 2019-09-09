---
title: O que é Privileged Identity Management? -O azure Active Directory | Documentos da Microsoft
description: Fornece uma visão geral de Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5009310dd4ea4e99e1da2871085ee2d2d99a6eab
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804503"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>O que é o Azure AD Privileged Identity Management?

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) é um serviço que permite gerenciar, controlar e monitorar o acesso a recursos importantes em sua organização. Tal inclui o acesso a recursos no Microsoft Azure AD, recursos do Azure e outros Microsoft Online Services, como o Office 365 ou o Microsoft Intune.

## <a name="why-should-i-use-pim"></a>Por que devo usar o PIM?

As organizações desejam minimizar o número de pessoas que têm acesso a informações ou recursos seguros, pois isso reduz a chance de um ator mal-intencionado obter esse acesso ou um usuário autorizado que afete inadvertidamente um recurso confidencial. No entanto, os utilizadores continuam a ter de realizar operações privilegiadas em aplicações do Azure AD, Azure, Office 365 ou SaaS. As organizações podem fornecer ao usuário acesso privilegiado JIT (just-in-time) aos recursos do Azure e ao Azure AD. Há uma necessidade de supervisão para o que esses usuários estão fazendo com seus privilégios de administrador. O PIM ajuda a reduzir o risco de direitos de acesso excessivas, desnecessários ou de uso indesejado.

## <a name="what-can-i-do-with-pim"></a>O que posso fazer com o PIM?

Essencialmente, o PIM ajuda a gerenciar quem, o que, quando, onde e por que os recursos se preocupam. Aqui estão alguns dos principais recursos do PIM:

- Fornecer acesso privilegiado **just-in-time** ao Azure AD e aos recursos do Azure
- Atribuir acesso com **limite de tempo** a recursos usando datas de início e término
- Exigir **aprovação** para ativar funções com privilégios
- Impor a **autenticação multifator** para ativar qualquer função
- Use a **justificação** para entender por que os usuários ativam
- Obter **notificações** quando funções com privilégios forem ativadas
- Realizar **revisões de acesso** para garantir que os usuários ainda precisem de funções
- Baixar o **histórico de auditoria** para auditoria interna ou externa

## <a name="terminology"></a>Terminologia

Para entender melhor o PIM e sua documentação, você deve examinar os termos a seguir.

| Termo ou conceito | Categoria de atribuição de função | Descrição |
| --- | --- | --- |
| direito | Type | Uma atribuição de função que exige que um usuário execute uma ou mais ações para usar a função. Se um usuário se tornou qualificado para uma função, isso significa que eles podem ativar a função quando precisam executar tarefas privilegiadas. Não há nenhuma diferença no acesso dado a alguém com uma atribuição de funções qualificada e permanente. A única diferença é que algumas pessoas não precisam desse acesso o tempo todo. |
| ativo | Type | Uma atribuição de função que não exige que um usuário execute qualquer ação para usar a função. Os usuários atribuídos como ativos têm os privilégios atribuídos à função. |
| ativar |  | O processo de executar uma ou mais ações para usar uma função à qual um usuário está qualificado. As ações podem incluir a execução de uma verificação de MFA (autenticação multifator), o fornecimento de uma justificativa de negócios ou a solicitação de aprovação de aprovadores designados. |
| associada | State | Um usuário que tem uma atribuição de função ativa. |
| ativado | State | Um usuário que tem uma atribuição de função qualificada, executou as ações para ativar a função e agora está ativo.  Depois de ativado, o usuário pode usar a função para um período de tempo pré-configurado antes de precisar ativá-lo novamente. |
| qualificado permanente | Duration | Uma atribuição de função em que um usuário sempre está qualificado para ativar a função. |
| ativo permanente | Duration | Uma atribuição de função em que um usuário sempre pode usar a função sem executar nenhuma ação. |
| expiração qualificada | Duration | Uma atribuição de função em que um usuário está qualificado para ativar a função em uma data de início e de término especificada. |
| expirar ativo | Duration | Uma atribuição de função em que um usuário pode usar a função sem executar nenhuma ação em uma data de início e de término especificada. |
| acesso just-in-time (JIT) |  | Um modelo no qual os usuários recebem permissões temporárias para executar tarefas privilegiadas, o que impede que usuários mal-intencionados ou não autorizados obtenham acesso depois que as permissões tiverem expirado. O acesso é concedido somente quando os usuários precisam dele. |
| princípio de acesso de privilégios mínimos |  | Uma prática de segurança recomendada na qual cada usuário é fornecido apenas com os privilégios mínimos necessários para realizar as tarefas que eles estão autorizados a executar. Essa prática minimiza o número de administradores globais e, em vez disso, usa funções de administrador específicas para determinados cenários. |

## <a name="what-does-pim-look-like"></a>Como é a aparência do PIM?

Depois de configurar o PIM, você verá as opções **tarefas**, **gerenciar**e **atividade** no menu de navegação à esquerda. Como administrador, você escolherá entre gerenciar **funções do Azure ad** e funções de **recursos do Azure** . Ao escolher o tipo de funções a serem gerenciadas, você verá um conjunto semelhante de opções para esse tipo de função.

![Captura de tela do PIM no portal do Azure](./media/pim-configure/pim-overview.png)

## <a name="who-can-do-what-in-pim"></a>Quem pode fazer o que no PIM?

Se você for a primeira pessoa a usar o PIM, você receberá automaticamente as funções [administrador de segurança](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e administrador de [função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) no diretório.

Para funções do Azure AD, somente um usuário que está na função de administrador de função com privilégios pode gerenciar atribuições para outros administradores no PIM. Você pode [conceder acesso a outros administradores para gerenciar o PIM](pim-how-to-give-access-to-pim.md). Administradores globais, administradores de segurança e leitores de segurança podem exibir atribuições às funções do Azure AD no PIM.

Para funções de recurso do Azure, somente um administrador de assinatura, um proprietário de recurso ou um administrador de acesso de usuário de recurso pode gerenciar atribuições para outros administradores no PIM. Os usuários que são administradores de função com privilégios, administradores de segurança ou leitores de segurança, por padrão, não têm acesso para exibir atribuições às funções de recurso do Azure no PIM.

## <a name="scenarios"></a>Cenários

O PIM dá suporte aos seguintes cenários:

**Enquanto Administrador de Função com Privilégios, pode:**

- Ativar a aprovação de funções específicas
- Especificar utilizadores e/ou grupos de aprovação para aprovar pedidos
- Ver o histórico de pedidos e de aprovação de todas as funções com privilégios

**Como Aprovador, você pode:**

- Ver aprovações pendentes (pedidos)
- Aprovar ou rejeitar pedidos para elevação de funções (individuais e/ou em massa)
- Fornecer justificação para aprovações/rejeições 

**Como um usuário de função elegível, você pode:**

- Pedir a ativação de uma função que requer aprovação
- Ver o estado do seu pedido para ativação
- Completar a sua tarefa no Azure AD se a ativação for aprovada

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Para obter informações sobre licenças para usuários, consulte [requisitos de licença para usar o PIM](subscription-requirements.md).

## <a name="next-steps"></a>Passos Seguintes

- [Requisitos de licença para usar o PIM](subscription-requirements.md)
- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implantar o PIM](pim-deployment-plan.md)
