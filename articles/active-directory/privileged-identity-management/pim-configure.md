---
title: O que é a Gestão de Identidade Privilegiada? - Azure AD [ Azure AD ] Microsoft Docs
description: Fornece uma visão geral da Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb0cc61b61328df86c27498a1007f2372fb9548
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81867453"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>O que é o Azure AD Privileged Identity Management?

Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) é um serviço que lhe permite gerir, controlar e monitorizar o acesso a recursos importantes na sua organização. Estes recursos incluem recursos em Azure AD, Azure e outros Serviços Online da Microsoft, como o Office 365 ou microsoft Intune.

## <a name="reasons-to-use"></a>Razões para usar

As organizações querem minimizar o número de pessoas que têm acesso a informações ou recursos seguros, porque isso reduz a probabilidade de um ator malicioso ter esse acesso, ou de um utilizador autorizado ter inadvertidamente impacto num recurso sensível. No entanto, os utilizadores continuam a ter de realizar operações privilegiadas em aplicações do Azure AD, Azure, Office 365 ou SaaS. As organizações podem dar aos utilizadores acesso privilegiado just-in-time (JIT) a recursos do Azure e ao Azure AD. É necessário que se descuide o que esses utilizadores estão a fazer com os seus privilégios de administrador.

## <a name="what-does-it-do"></a>O que é que faz?

A Privileged Identity Management fornece ativação de funções baseadano no tempo e na aprovação para mitigar os riscos de permissões de acesso excessivas, desnecessárias ou mal utilizadas sobre recursos que lhe interessam. Estas são algumas das principais características da Gestão de Identidade Privilegiada:

- Fornecer acesso privilegiado **just-in-time** aos recursos Da Azure AD e Azure
- Atribuir acesso limitado ao **tempo** aos recursos utilizando datas de início e fim
- Exigir **aprovação** para ativar papéis privilegiados
- Impor **a autenticação de vários fatores** para ativar qualquer função
- Use **a justificação** para entender por que os utilizadores ativam
- Obtenha **notificações** quando as funções privilegiadas forem ativadas
- Realizar **avaliações** de acesso para garantir que os utilizadores ainda precisam de funções
- Descarregue **o histórico** de auditoria para auditoria interna ou externa

## <a name="what-can-i-do-with-it"></a>O que posso fazer com isto?

Assim que configurar a Gestão de Identidade Privilegiada, verá **tarefas,** **gerir**e **opções** de Atividade no menu de navegação à esquerda. Como administrador, escolherá entre gerir **funções de AD Azure** e funções de **recurso Azure.** Ao escolher o tipo de funções a gerir, vê um conjunto de opções semelhantes para esse tipo de função.

![Screenshot da Gestão de Identidade Privilegiada no portal Azure](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Quem pode fazer o quê?

Para funções de AD Azure na Gestão de Identidade Privilegiada, apenas um utilizador que esteja no cargo de administrador privilegiado ou administrador global pode gerir atribuições para outros administradores. Pode [conceder acesso a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md) Administradores Globais, Administradores de Segurança, leitores globais e leitores de segurança também podem ver atribuições a funções da Azure AD na Gestão de Identidade Privilegiada.

Para funções de recursos Azure na Gestão de Identidade Privilegiada, apenas um administrador de subscrição, um proprietário de recursos ou um administrador de acesso ao utilizador de recursos pode gerir atribuições para outros administradores. Os utilizadores que sejam Administradores de Papel Privilegiado, Administradores de Segurança ou Leitores de Segurança não têm, por defeito, acesso a tarefas de visualização de funções de recursos Azure na Gestão de Identidade Privilegiada.

## <a name="scenarios"></a>Cenários

O Privileged Identity Management suporta os seguintes cenários:

### <a name="privileged-role-administrator-permissions"></a>Permissões de administrador de funções privilegiadas

- Ativar a aprovação de funções específicas
- Especificar utilizadores ou grupos aprovadores para aprovar pedidos
- Ver o histórico de pedidos e de aprovação de todas as funções com privilégios

### <a name="approver-permissions"></a>Permissões de aprovação

- Ver aprovações pendentes (pedidos)
- Aprovar ou rejeitar pedidos de elevação de funções (individuais e a granel)
- Fornecer justificação para a minha aprovação ou rejeição

### <a name="eligible-role-user-permissions"></a>Permissões elegíveis do utilizador

- Pedir a ativação de uma função que requer aprovação
- Ver o estado do seu pedido para ativação
- Completar a sua tarefa no Azure AD se a ativação for aprovada

## <a name="terminology"></a>Terminologia

Para melhor compreender a Gestão de Identidade Privilegiada e a sua documentação, deve rever os seguintes termos.

| Termo ou conceito | Categoria de atribuição de funções | Descrição |
| --- | --- | --- |
| elegível | Tipo | Uma atribuição de funções que requer que um utilizador realize uma ou mais ações para usar o papel. Se um utilizador tiver sido elegível para um papel, isso significa que pode ativar a função quando precisa de executar tarefas privilegiadas. Não há diferença no acesso dado a alguém com um papel permanente contra um papel elegível. A única diferença é que algumas pessoas não precisam de ter acesso a toda a hora. |
| ativo | Tipo | Uma atribuição de funções que não requer que um utilizador execute qualquer ação para usar o papel. Os utilizadores designados como ativos têm os privilégios atribuídos ao papel. |
| ativar |  | O processo de realização de uma ou mais ações para utilizar uma função para a qual um utilizador é elegível. As ações podem incluir a realização de um controlo de autenticação multifactor (MFA), o fornecimento de uma justificação de negócio, ou o pedido de aprovação dos aprovadores designados. |
| atribuído | Estado | Um utilizador que tem uma atribuição de papel ativo. |
| ativado | Estado | Um utilizador que tem uma atribuição de funções elegíveis, realizou as ações para ativar o papel, e agora está ativo.  Uma vez ativado, o utilizador pode utilizar a função para um período de tempo pré-configurado antes de ter de voltar a ativar. |
| elegível permanente | Duração | Uma atribuição de funções em que um utilizador é sempre elegível para ativar a função. |
| permanente ativo | Duração | Uma atribuição de funções onde um utilizador pode sempre usar o papel sem realizar quaisquer ações. |
| expirar elegível | Duração | Uma atribuição de funções em que um utilizador é elegível para ativar a função dentro de uma data de início e fim especificada. |
| expirar ativo | Duração | Uma atribuição de funções onde um utilizador pode usar a função sem realizar quaisquer ações dentro de uma data de início e fim especificada. |
| acesso just-in-time (JIT) |  | Um modelo no qual os utilizadores recebem permissões temporárias para executar tarefas privilegiadas, o que impede que utilizadores maliciosos ou não autorizados tenham acesso após o termo das permissões. O acesso só é concedido quando os utilizadores precisam. |
| princípio de acesso menos privilegiado |  | Uma prática de segurança recomendada na qual cada utilizador é dotado apenas dos privilégios mínimos necessários para realizar as tarefas que está autorizado a executar. Esta prática minimiza o número de Administradores Globais e, em vez disso, utiliza funções específicas de administrador para determinados cenários. |

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Para obter informações sobre licenças para utilizadores, consulte [os requisitos da Licença para utilizar a Gestão de Identidade Privilegiada.](subscription-requirements.md)

## <a name="next-steps"></a>Passos seguintes

- [Requisitos de licença para usar Gestão de Identidade Privilegiada](subscription-requirements.md)
- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implementar o Privileged Identity Management](pim-deployment-plan.md)
