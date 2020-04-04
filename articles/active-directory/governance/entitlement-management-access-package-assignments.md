---
title: Ver, adicionar e remover atribuições para um pacote de acesso na gestão de direitos da Azure AD - Diretório Ativo Azure
description: Saiba como visualizar, adicionar e remover atribuições para um pacote de acesso na gestão de direitos do Diretório Ativo Azure.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12e3b86f41e7188778393ab717554907ef5d44ec
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631734"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Ver, adicionar e remover atribuições para um pacote de acesso na gestão de direitos da AD Azure

Na gestão de direitos da Azure AD, pode ver quem foi designado para aceder a pacotes, a sua política e estatuto. Se um pacote de acesso tiver uma política adequada, também pode atribuir diretamente ao utilizador um pacote de acesso. Este artigo descreve como visualizar, adicionar e remover atribuições para pacotes de acesso.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a gestão de direitos da Azure AD e atribuir aos utilizadores pacotes de acesso, deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Licença de Mobilidade Empresarial + Segurança (EMS) E5

## <a name="view-who-has-an-assignment"></a>Ver quem tem uma missão

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **Atribuições** para ver uma lista de atribuições ativas.

    ![Lista de atribuições a um pacote de acesso](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Clique numa atribuição específica para ver detalhes adicionais.

1. Para ver uma lista de atribuições que não tivessem todas as funções de recurso devidamente aprovisionadas, clique no estado do filtro e **selecione Delivering**.

    Pode ver detalhes adicionais sobre os erros de entrega, localizando o pedido correspondente do utilizador na página **de Pedidos.**

1. Para ver as atribuições vencidas, clique no estado do filtro e **selecione Expirado**.

1. Para descarregar um ficheiro CSV da lista filtrada, clique em **Baixar**.

### <a name="viewing-assignments-programmatically"></a>Tarefas de visualização programática

Também pode recuperar atribuições num pacote de acesso utilizando o Microsoft Graph.  Um utilizador numa função adequada com uma `EntitlementManagement.ReadWrite.All` aplicação que tenha a permissão delegada pode ligar para a API para [listar acessoS PacoteS](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Atribuir diretamente um utilizador

Em alguns casos, é possível atribuir diretamente utilizadores específicos a um pacote de acesso para que os utilizadores não tenham de passar pelo processo de solicitação do pacote de acesso. Para atribuir diretamente aos utilizadores, o pacote de acesso deve ter uma política que permita atribuições diretas ao administrador.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu esquerdo, clique em **Tarefas**.

1. Clique em **Nova atribuição** para abrir Adicionar o utilizador ao pacote de acesso.

    ![Atribuições - Adicionar o utilizador ao pacote de acesso](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Clique em **Adicionar utilizadores** para selecionar os utilizadores a quem pretende atribuir este pacote de acesso.

1. Na lista **de políticas Select,** selecione uma política que os futuros pedidos dos utilizadores e o ciclo de vida serão regidos e rastreados. Se pretender que os utilizadores selecionados tenham diferentes definições de política, pode clicar **em Criar uma nova política** para adicionar uma nova política.

1. Detete a data e a hora em que pretende que a atribuição dos utilizadores selecionados comece e termine. Se não for fornecida uma data de fim, as definições do ciclo de vida da apólice serão utilizadas.

1. Opcionalmente, forneça uma justificação para a sua atribuição direta para a manutenção de registos.

1. Clique **em Adicionar** para atribuir diretamente os utilizadores selecionados ao pacote de acesso.

    Após alguns momentos, clique em **Refresh** para ver os utilizadores na lista de Tarefas.

### <a name="directly-assigning-users-programmatically"></a>Atribuição direta dos utilizadores programáticamente

Também pode atribuir diretamente um utilizador a um pacote de acesso utilizando o Microsoft Graph.  Um utilizador numa função adequada com uma `EntitlementManagement.ReadWrite.All` aplicação que tenha a permissão delegada pode ligar para a API para [criar um AccessPackageAssignmentRequest](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Remover uma atribuição

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu esquerdo, clique em **Tarefas**.
 
1. Clique na caixa de verificação ao lado do utilizador cuja atribuição pretende remover do pacote de acesso. 

1. Clique no botão **Remover** perto da parte superior do painel esquerdo. 
 
    ![Atribuições - Remova o utilizador do pacote de acesso](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Aparecerá uma notificação informando-o de que a atribuição foi removida. 

## <a name="next-steps"></a>Passos seguintes

- [Alterar pedido e configurações para um pacote de acesso](entitlement-management-access-package-request-policy.md)
- [Ver relatórios e registos](entitlement-management-reports.md)
