---
title: O que é o Privileged Identity Management? -O azure Active Directory | Documentos da Microsoft
description: Fornece uma visão geral do Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0678dc42d8c22c8dcc20137cf70f81a1eb4f61c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473695"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>O que é o Azure AD Privileged Identity Management?

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) é um serviço que lhe permite gerir, controlar e monitorizar o acesso a recursos importantes na sua organização. Tal inclui o acesso a recursos no Microsoft Azure AD, recursos do Azure e outros Microsoft Online Services, como o Office 365 ou o Microsoft Intune.

## <a name="why-should-i-use-pim"></a>Por que motivo devo utilizar o PIM?

As organizações querem minimizar o número de pessoas que têm acesso para proteger informações ou recursos, uma vez que reduz a chance de um ator malicioso esse acesso ou um utilizador autorizado inadvertidamente afetar um recurso confidencial. No entanto, os utilizadores continuam a ter de realizar operações privilegiadas em aplicações do Azure AD, Azure, Office 365 ou SaaS. As organizações podem dar aos utilizadores acesso a recursos do Azure e o Azure AD de privilegiado just-in-time (JIT). Há uma necessidade de supervisão para o que os utilizadores estão a fazer com seus privilégios de administrador. PIM ajuda a mitigar o risco de excessiva, desnecessários ou indevidamente direitos de acesso.

## <a name="what-can-i-do-with-pim"></a>O que posso fazer com o PIM?

PIM essencialmente ajuda a gerenciar o quem, o que, quando, onde e por que motivo para recursos que mais lhe interessa. Aqui estão alguns dos principais recursos do PIM:

- Fornecer **just-in-time** privilegiado acesso ao Azure AD e recursos do Azure
- Atribua **com limite de tempo** acesso a recursos através de datas de início e de fim
- Exigir **aprovação** para ativar as funções com privilégios
- Impor **multi-factor authentication** para ativar qualquer função
- Uso **justificação** para compreender por que os utilizadores de ativar
- Obtenha **notificações** quando são ativadas funções privilegiadas
- Conduzir **as revisões de acesso** para garantir que os utilizadores ainda têm de funções
- Baixe **histórico de auditorias** para auditoria interna ou externa

## <a name="terminology"></a>Terminologia

Para compreender melhor o PIM e a respetiva documentação, deve rever os termos seguintes.

| Termo ou conceito | Categoria de atribuição de função | Descrição |
| --- | --- | --- |
| Elegível | Type | Uma atribuição de função que requer que um utilizador efetuar uma ou mais ações para utilizar a função. Se um utilizador se tornou qualificado para uma função, o que significa que os utilizadores podem ativar a função quando precisam de executar tarefas com privilégios. Não existe nenhuma diferença no acesso concedido a qualquer pessoa com uma permanente em comparação com uma atribuição de função elegível. A única diferença é que, algumas pessoas não precisam que o acesso o tempo todo. |
| ativo | Type | Uma atribuição de função que não necessita de um utilizador efetuar qualquer ação para utilizar a função. Os utilizadores atribuídos como ativo têm os privilégios atribuídos à função. |
| ativar |  | O processo de efetuar uma ou mais ações para utilizar uma função que é elegível para um utilizador. Ações podem incluir a efetuar uma verificação de autenticação multifator (MFA), fornecer uma justificação de negócio ou o pedido de aprovação de aprovadores designados. |
| Atribuído | Estado | Um utilizador com uma atribuição de função do Active Directory. |
| Ativado | Estado | Um utilizador com uma atribuição de função elegível, efetuar as ações para ativar a função e agora está ativo.  Depois de ativado, o utilizador pode utilizar a função para um período de-de-período de tempo pré-configurado antes de que precisam de ativar novamente. |
| elegível permanente | Duração | Uma atribuição de função em que um usuário sempre é elegível para ativar a função. |
| Ativa permanente | Duração | Uma atribuição de função em que um utilizador pode sempre utilizar a função sem realizar qualquer ação. |
| expirar elegíveis | Duração | Uma atribuição de função em que um utilizador é elegível para ativar a função dentro de uma data de início e de fim especificada. |
| expirar Active Directory | Duração | Uma atribuição de função em que um utilizador pode utilizar a função sem executar quaisquer ações dentro de uma data de início e de fim especificada. |
| acesso just-in-time (JIT) |  | Um modelo no qual os utilizadores recebem permissões temporárias para efetuar tarefas com privilégios, que impede que os utilizadores não autorizados ou mal-intencionados de obter acesso depois das permissões tiverem expirado. O acesso é concedido somente quando os utilizadores a necessidade. |
| princípio de acesso de privilégio mínimo |  | Uma prática recomendada de segurança em que cada utilizador é fornecido com apenas os privilégios mínimos necessários para realizar as tarefas que estão autorizados a efetuar. Esta prática minimiza o número de administradores globais e em vez disso, utiliza funções de administrador específicas para determinados cenários. |

## <a name="what-does-pim-look-like"></a>O que o aspeto de PIM?

Depois de configurar o PIM, verá **tarefas**, **gerir**, e **atividade** opções no menu de navegação esquerdo. Como administrador, que vai escolher entre o gerenciamento **funções do Azure AD** e **recursos do Azure** funções. Ao escolher o tipo de funções para gerir, verá um conjunto de opções para esse tipo de função semelhantes.

![Captura de ecrã do PIM no portal do Azure](./media/pim-configure/pim-overview.png)

## <a name="who-can-do-what-in-pim"></a>Quem pode fazer o que no PIM?

Se for a primeira pessoa a utilizar o PIM, é-lhe automaticamente atribuída a [administrador de segurança](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e [administrador com função privilegiada](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) funções no diretório.

Para funções do Azure AD, apenas um utilizador que está na função de administrador com função privilegiada pode gerir atribuições de outros administradores no PIM. Pode [conceder acesso a outros administradores para gerir o PIM](pim-how-to-give-access-to-pim.md). Administradores globais, administradores de segurança e leitores de segurança podem ver as atribuições de funções do Azure AD PIM.

Para funções de recursos do Azure, apenas um administrador de subscrição, um proprietário do recurso ou um recurso de administrador de acesso de utilizador pode gerir atribuições de outros administradores no PIM. Os utilizadores que são administradores de função com privilégios, os administradores de segurança ou leitores de segurança, não por predefinição, tem acesso para ver as atribuições a funções de recursos do Azure no PIM.

## <a name="scenarios"></a>Cenários

PIM suporta os seguintes cenários:

**Enquanto Administrador de Função com Privilégios, pode:**

- Ativar a aprovação de funções específicas
- Especificar utilizadores e/ou grupos de aprovação para aprovar pedidos
- Ver o histórico de pedidos e de aprovação de todas as funções com privilégios

**Como um aprovador, pode:**

- Ver aprovações pendentes (pedidos)
- Aprovar ou rejeitar pedidos para elevação de funções (individuais e/ou em massa)
- Fornecer justificação para aprovações/rejeições 

**Como um utilizador de função elegível, pode:**

- Pedir a ativação de uma função que requer aprovação
- Ver o estado do seu pedido para ativação
- Completar a sua tarefa no Azure AD se a ativação for aprovada

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Para obter informações sobre as licenças para os utilizadores, consulte [os requisitos para utilizar o PIM de licença](subscription-requirements.md).

## <a name="next-steps"></a>Passos Seguintes

- [Requisitos de licença para utilizar o PIM](subscription-requirements.md)
- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implementar o PIM](pim-deployment-plan.md)
