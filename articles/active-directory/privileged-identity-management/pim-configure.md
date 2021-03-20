---
title: O que é Gestão de Identidade Privilegiada? - Azure AD | Microsoft Docs
description: Fornece uma visão geral da Azure AD Gestão de Identidade Privilegiada (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 09/29/2020
ms.author: curtand
ms.custom: pim ; azuread-video-2020
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c7003ce3e8c65f6fdb7f3d847947114aee251f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370190"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>O que é o Azure AD Privileged Identity Management?

 Gestão de Identidade Privilegiada (PIM) é um serviço no Azure Ative Directory (Azure AD) que lhe permite gerir, controlar e monitorizar o acesso a recursos importantes na sua organização. Estes recursos incluem recursos em Azure AD, Azure e outros serviços Microsoft Online, como o Microsoft 365 ou o Microsoft Intune. O vídeo que se segue apresenta-o a importantes conceitos e funcionalidades pim.
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>Razões para usar

As organizações querem minimizar o número de pessoas que têm acesso a informações ou recursos seguros, porque isso reduz a probabilidade de um ator malicioso ter esse acesso, ou de um utilizador autorizado inadvertidamente impactar um recurso sensível. No entanto, os utilizadores ainda precisam de realizar operações privilegiadas em ad AD, Azure, Microsoft 365 ou SaaS. As organizações podem dar aos utilizadores acesso privilegiado aos recursos Azure e Azure AD. É necessário um controlo sobre o que esses utilizadores estão a fazer com os seus privilégios de administrador.

## <a name="what-does-it-do"></a>O que é que faz?

A Gestão de Identidade Privilegiada fornece ativação de funções baseadas no tempo e na aprovação para mitigar os riscos de permissões de acesso excessivas, desnecessárias ou mal utilizadas em recursos que lhe interessam. Estas são algumas das principais características da Gestão de Identidade Privilegiada:

- Fornecer acesso privilegiado **just-in-time** aos recursos Azure AD e Azure
- Atribuir acesso **vinculado a** recursos com datas de início e fim
- Requer **aprovação** para ativar funções privilegiadas
- Impor **a autenticação de vários fatores** para ativar qualquer papel
- Use **a justificação** para entender por que os utilizadores ativam
- Receba **notificações** quando as funções privilegiadas são ativadas
- Realizar **revisões de acesso** para garantir que os utilizadores ainda precisam de papéis
- Baixe **o histórico de auditoria** para auditoria interna ou externa

## <a name="what-can-i-do-with-it"></a>O que posso fazer com isto?

Assim que configurar a Gestão de Identidade Privilegiada, verá as opções **de Tarefas,** **Gestão** e **Atividade** no menu de navegação à esquerda. Como administrador, você escolherá entre opções como gerir **funções Azure AD,** gerir funções **de recursos Azure** ou grupos de acesso privilegiados. Quando escolhe o que quer gerir, vê o conjunto de opções apropriado para essa opção.

![Screenshot da Gestão de Identidade Privilegiada no portal Azure](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Quem pode fazer o quê?

Para as funções de Azure AD em Gestão de Identidade Privilegiada, apenas um utilizador que esteja na função de administrador privilegiado ou administrador global pode gerir atribuições para outros administradores. Pode [conceder acesso a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md) Administradores globais, administradores de segurança, leitores globais e leitores de segurança também podem ver atribuições para funções AZure AD em Gestão de Identidade Privilegiada.

Para as funções de recursos do Azure na Gestão de Identidade Privilegiada, apenas um administrador de subscrição, um titular de recursos ou um administrador de acesso ao utilizador de recursos podem gerir atribuições para outros administradores. Os utilizadores que são Administradores de Funções Privilegiados, Administradores de Segurança ou Leitores de Segurança não têm, por defeito, acesso a ver atribuições a funções de recursos Azure na Gestão de Identidade Privilegiada.

## <a name="scenarios"></a>Cenários

O Privileged Identity Management suporta os seguintes cenários:

### <a name="privileged-role-administrator-permissions"></a>Permissões privilegiadas de administrador de funções

- Ativar a aprovação de funções específicas
- Especificar utilizadores ou grupos de aprovadores para aprovar pedidos
- Ver o histórico de pedidos e de aprovação de todas as funções com privilégios

### <a name="approver-permissions"></a>Permissões de aprovação

- Ver aprovações pendentes (pedidos)
- Aprovar ou rejeitar pedidos de elevação de funções (simples e a granel)
- Apresentar justificação para a minha aprovação ou rejeição

### <a name="eligible-role-user-permissions"></a>Permissões elegíveis para utilizador de funções

- Pedir a ativação de uma função que requer aprovação
- Ver o estado do seu pedido para ativação
- Completar a sua tarefa no Azure AD se a ativação for aprovada

## <a name="terminology"></a>Terminologia

Para melhor compreender a Gestão de Identidade Privilegiada e a sua documentação, deverá rever os seguintes termos.

| Termo ou conceito | Categoria de atribuição de funções | Description |
| --- | --- | --- |
| elegível | Tipo | Uma atribuição de funções que requer que um utilizador execute uma ou mais ações para usar o papel. Se um utilizador tiver sido elegível para um papel, isso significa que pode ativar o papel quando precisa de executar tarefas privilegiadas. Não há diferença no acesso dado a alguém com uma função permanente contra uma função elegível. A única diferença é que algumas pessoas não precisam sempre desse acesso. |
| active | Tipo | Uma tarefa que não requer que um utilizador execute qualquer ação para usar o papel. Os utilizadores atribuídos como ativos têm os privilégios atribuídos ao papel. |
| ativar |  | O processo de realização de uma ou mais ações para usar um papel para o qual um utilizador é elegível. As ações podem incluir a realização de uma verificação de autenticação multi-factor (MFA), a justificação do negócio ou a solicitação de aprovação dos aprovadores designados. |
| atribuído | Estado | Um utilizador que tem uma atribuição de função ativa. |
| ativado | Estado | Um utilizador que tenha uma atribuição de papel elegível, executou as ações para ativar o papel, e está agora ativo.  Uma vez ativado, o utilizador pode utilizar a função para um período de tempo pré-configurado antes de necessitar de ser novamente ativado. |
| elegível permanente | Duração | Uma atribuição de funções em que um utilizador é sempre elegível para ativar o papel. |
| permanente ativo | Duração | Uma atribuição de funções em que um utilizador pode sempre usar o papel sem realizar quaisquer ações. |
| expirar elegível | Duração | Uma atribuição de funções em que um utilizador é elegível para ativar a função dentro de uma data de início e fim especificada. |
| expirar ativo | Duração | Uma atribuição de funções em que um utilizador pode usar o papel sem realizar quaisquer ações dentro de uma data de início e fim especificada. |
| acesso just-in-time (JIT) |  | Um modelo em que os utilizadores recebem permissões temporárias para executar tarefas privilegiadas, o que impede que utilizadores maliciosos ou não autorizados tenham acesso após o termo das permissões. O acesso só é concedido quando os utilizadores precisam. |
| princípio de acesso menos privilegiado |  | Uma prática de segurança recomendada na qual cada utilizador é dotado apenas dos privilégios mínimos necessários para realizar as tarefas que está autorizado a executar. Esta prática minimiza o número de Administradores Globais e, em vez disso, utiliza funções específicas de administrador para determinados cenários. |

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Para obter informações sobre licenças para utilizadores, consulte [os requisitos da Licença para utilizar a Gestão de Identidade Privilegiada.](subscription-requirements.md)

## <a name="next-steps"></a>Passos seguintes

- [Requisitos de licença para utilizar a Gestão de Identidade Privilegiada](subscription-requirements.md)
- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implementar o Privileged Identity Management](pim-deployment-plan.md)
