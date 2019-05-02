---
title: Ver os registos e relatórios na gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Saiba como ver o relatório de atribuições de utilizador e de registos de auditoria na gestão de direitos do Azure Active Directory (pré-visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541544"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Ver relatórios e os registos na gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Ver recursos de que um utilizador tem acesso

1. Clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **relatório de atribuições de utilizador**.

1. Clique em **selecionar utilizadores** para abrir o painel de utilizadores selecione.

1. Localize o utilizador na lista que pretende ver os recursos que aos quais têm acesso.

1. Clique com o utilizador e, em seguida, clique em **selecione**.

    É apresentada uma lista de recursos que o utilizador tem acesso. Ele inclui o pacote de acesso, política e datas.

    ![Relatório de atribuições de utilizador](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Determinar o estado do pedido de um utilizador

Para obter detalhes adicionais sobre como um utilizador pediu e teve acesso a um pacote de acesso, pode utilizar o log de auditoria do Azure AD. Em particular, pode usar os registros de log no `EntitlementManagement` e `UserManagement` categorias para obter detalhes adicionais sobre os passos de processamento para cada solicitação.  

1. Clique em **do Azure Active Directory** e, em seguida, clique em **registos de auditoria**.

1. Na parte superior, altere a **categoria** a qualquer uma `EntitlementManagement` ou `UserManagement`, consoante o registo de auditoria que procura.  

1. Clique em **Aplicar**.

1. Para transferir os registos, clique em **transferir**.

Quando o Azure AD recebe um novo pedido, ele grava um registo de auditoria, em que o **categoria** é `EntitlementManagement` e o **atividade** é normalmente `User requests access package assignment`.  No caso de uma atribuição direta criada no portal do Azure, o **atividade** campo do registo de auditoria é `Administrator directly assigns user to access package`, e o utilizador que executa a atribuição é identificado pelo **ActorUserPrincipalName**.

Azure AD irá escrever os registos de auditoria adicional enquanto o pedido está em curso, incluindo:

| Category | Atividade | Estado do pedido |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Pedido não necessita de aprovação |
| `UserManagement` | `Create request approval` | Pedido requer aprovação |
| `UserManagement` | `Add approver to request approval` | Pedido requer aprovação |
| `EntitlementManagement` | `Approve access package assignment request` | Pedido aprovado |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Pedido aprovado ou não necessita de aprovação |

Quando um utilizador é atribuído acesso, o Azure AD escreve um registo de auditoria para o `EntitlementManagement` categoria com **atividade** `Fulfill access package assignment`.  O utilizador que receberam o acesso é identificado pela **ActorUserPrincipalName** campo.

Se o acesso não foi atribuído, então do Azure AD escreve um registo de auditoria para o `EntitlementManagement` categoria com **atividade** qualquer um dos `Deny access package assignment request`, se o pedido foi negado por um aprovador, ou `Access package assignment request timed out (no approver action taken)`, se o pedido excedeu o limite de tempo antes de um pode aprovar o aprovador.

Quando expira a atribuição de pacote de acesso do utilizador, é cancelado pelo utilizador ou removido por um administrador do Azure AD escreve um registo de auditoria para o `EntitlementManagement` categoria com **atividade** de `Remove access package assignment`.

## <a name="next-steps"></a>Passos Seguintes

- [Resolver problemas de gestão de direitos do Azure AD](entitlement-management-troubleshoot.md)
- [Cenários comuns](entitlement-management-scenarios.md)
