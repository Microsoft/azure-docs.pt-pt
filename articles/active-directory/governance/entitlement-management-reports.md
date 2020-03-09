---
title: Ver relatórios e registos na gestão de direitos - Azure AD
description: Saiba como visualizar o relatório de atribuição de utilizadores e os registos de auditoria na gestão de direitos do Diretório Ativo azure.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: c98a583e2aa8ac679842e16d1c0cc36811db90de
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376697"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Ver relatórios e registos na gestão de direitos da Azure AD

Os relatórios de gestão de direitos da AD Azure e o registo de auditoria da Azure AD fornecem detalhes adicionais sobre os recursos a que os utilizadores têm acesso. Como administrador, pode visualizar os pacotes de acesso e atribuições de recursos para um utilizador e visualizar os registos de pedidos para fins de auditoria ou determinar o estado do pedido de um utilizador. Este artigo descreve como utilizar os relatórios de gestão de direitos e os registos de auditoria da Azure AD.

Veja o seguinte vídeo para saber a que recursos os utilizadores têm acesso na gestão de direitos:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Ver pacotes de acesso para um utilizador

Este relatório permite-lhe listar todos os pacotes de acesso que um utilizador pode solicitar e os pacotes de acesso que estão atualmente atribuídos ao utilizador.

**Papel pré-requisito:** Administrador global ou administrador de utilizador

1. Clique em **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Relatórios**.

1. Clique em **pacotes**de acesso para um utilizador .

1. Clique em **Selecionar utilizadores** para abrir o painel de utilizadores Select.

1. Encontre o utilizador na lista e, em seguida, clique em **Selecionar**.

    O separador **Can solicita** apresentar uma lista dos pacotes de acesso que o utilizador pode solicitar. Esta lista é determinada pelas políticas de [pedido](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) definidas para os pacotes de acesso. 

    ![Pacotes de acesso para um utilizador](./media/entitlement-management-reports/access-packages-report.png)

1. Se houver mais de um recurso ou políticas para um pacote de acesso, clique nas funções de recurso ou na entrada de políticas para ver detalhes de seleção.

1. Clique no separador **Atribuído** para ver uma lista dos pacotes de acesso atualmente atribuídos ao utilizador. Quando um pacote de acesso é atribuído a um utilizador, significa que o utilizador tem acesso a todas as funções de recurso no pacote de acesso.

## <a name="view-resource-assignments-for-a-user"></a>Ver atribuições de recursos para um utilizador

Este relatório permite-lhe listar os recursos atualmente atribuídos a um utilizador na gestão de direitos. Note-se que este relatório é para os recursos geridos com a gestão de direitos. O utilizador poderá ter acesso a outros recursos no seu diretório fora da gestão de direitos.

**Papel pré-requisito:** Administrador global ou administrador de utilizador

1. Clique em **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Relatórios**.

1. Clique em **atribuições de Recursos para um utilizador**.

1. Clique em **Selecionar utilizadores** para abrir o painel de utilizadores Select.

1. Encontre o utilizador na lista e, em seguida, clique em **Selecionar**.

    É apresentada uma lista dos recursos atualmente atribuídos ao utilizador. A lista mostra também o pacote de acesso e a política de que obtiveram o papel de recurso, juntamente com a data de início e fim para acesso.
    
    Se um utilizador tiver acesso ao mesmo recurso em dois ou mais pacotes, pode clicar numa seta para ver cada pacote e política.

    ![Atribuições de recursos para um utilizador](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Determinar o estado do pedido de um utilizador

Para obter detalhes adicionais sobre como um utilizador solicitou e recebeu acesso a um pacote de acesso, pode utilizar o registo de auditoria da AD Azure. Em particular, pode utilizar os registos de registo nas categorias `EntitlementManagement` e `UserManagement` para obter detalhes adicionais sobre as etapas de processamento para cada pedido.  

1. Clique em **Diretório Ativo Azure** e, em seguida, clique em **registos de auditoria**.

1. No topo, mude a **categoria** para `EntitlementManagement` ou `UserManagement`, dependendo do registo de auditoria que procura.  

1. Clique em **Aplicar**.

1. Para descarregar os registos, clique em **Baixar**.

Quando a Azure AD recebe um novo pedido, escreve um registo de auditoria, no qual a **categoria** é `EntitlementManagement` e a **Atividade** é tipicamente `User requests access package assignment`.  No caso de uma atribuição direta criada no portal Azure, o campo **de atividade** do registo de auditoria é `Administrator directly assigns user to access package`, e o utilizador que executa a atribuição é identificado pelo **Nome ActorUserPrincipal**.

A Azure AD escreverá registos de auditoria adicionais enquanto o pedido estiver em curso, incluindo:

| Categoria | Atividade | Estado do pedido |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Pedido não requer aprovação |
| `UserManagement` | `Create request approval` | Pedido requer aprovação |
| `UserManagement` | `Add approver to request approval` | Pedido requer aprovação |
| `EntitlementManagement` | `Approve access package assignment request` | Pedido aprovado |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Pedido aprovado ou não requer aprovação |

Quando um utilizador é atribuído acesso, a Azure AD escreve um registo de auditoria para a categoria `EntitlementManagement` com `Fulfill access package assignment`de **Atividade** .  O utilizador que recebeu o acesso é identificado pelo campo **ActorUserPrincipalName.**

Se o acesso não for atribuído, a Azure AD escreve um registo de auditoria para a categoria `EntitlementManagement` com **atividade** `Deny access package assignment request`, se o pedido foi negado por um aprovador, ou `Access package assignment request timed out (no approver action taken)`, se o pedido tiver sido cronometrado antes de um aprovador poder aprovar.

Quando a atribuição do pacote de acesso do utilizador expirar, é cancelada pelo utilizador, ou removida por um administrador, então a Azure AD escreve um registo de auditoria para a categoria `EntitlementManagement` com **atividade** de `Remove access package assignment`.

## <a name="next-steps"></a>Passos seguintes

- [Relatórios de arquivo e registos](entitlement-management-logs-and-reporting.md)
- [Resolução de problemas Azure Gestão de direitos da AD](entitlement-management-troubleshoot.md)
- [Cenários comuns](entitlement-management-scenarios.md)
