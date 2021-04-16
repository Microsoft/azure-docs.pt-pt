---
title: Ver, adicionar e remover atribuições para um pacote de acesso na gestão de direitos AD Azure - Azure Ative Directory
description: Saiba como ver, adicionar e remover atribuições para um pacote de acesso na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e00fe3761824462252ce4984beb754385f3eca9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532157"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Ver, adicionar e remover atribuições para um pacote de acesso na gestão de direitos AD Azure

Na gestão de direitos da AD Azure, você pode ver quem foi designado para aceder a pacotes, sua política e estado. Se um pacote de acesso tiver uma política adequada, também pode atribuir diretamente o utilizador a um pacote de acesso. Este artigo descreve como ver, adicionar e remover atribuições para pacotes de acesso.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a gestão de direitos AD da Azure e atribuir aos utilizadores o acesso a pacotes, deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Licença E5 do Enterprise Mobility + Security (EMS)

## <a name="view-who-has-an-assignment"></a>Ver quem tem uma missão

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo, gestor de pacotes de acesso ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. Clique **em Atribuições** para ver uma lista de atribuições ativas.

    ![Lista de atribuições a um pacote de acesso](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Clique numa atribuição específica para ver detalhes adicionais.

1. Para ver uma lista de atribuições que não tenham todas as funções de recursos devidamente aprovisionadas, clique no estado do filtro e selecione **Entregar**.

    Pode ver detalhes adicionais sobre erros de entrega localizando o pedido correspondente do utilizador na página **'Pedidos'.**

1. Para ver as atribuições expiradas, clique no estado do filtro e selecione **Expirado**.

1. Para descarregar um ficheiro CSV da lista filtrada, clique em **Baixar**.

### <a name="viewing-assignments-programmatically"></a>Visualização de atribuições programáticamente

Também pode obter atribuições num pacote de acesso utilizando o Microsoft Graph.  Um utilizador numa função adequada com uma aplicação que tenha a permissão delegada `EntitlementManagement.ReadWrite.All` pode ligar para a API para [listar acessosEmtribuimentos](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true).

## <a name="directly-assign-a-user"></a>Atribuir diretamente a um utilizador

Em alguns casos, é melhor atribuir diretamente utilizadores específicos a um pacote de acesso para que os utilizadores não tenham de passar pelo processo de solicitação do pacote de acesso. Para atribuir diretamente aos utilizadores, o pacote de acesso deve ter uma política que permita atribuições diretas ao administrador.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo, gestor de pacotes de acesso ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. No menu esquerdo, clique em **Atribuições**.

1. Clique **em Nova atribuição** para abrir Adicionar pacote de acesso ao utilizador.

    ![Atribuições - Adicionar ao utilizador para aceder ao pacote](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Clique **em Adicionar utilizadores** para selecionar os utilizadores a quem pretende atribuir este pacote de acesso.

1. Na lista **de política Select,** selecione uma política que os futuros pedidos e ciclo de vida dos utilizadores serão regidos e rastreados. Se quiser que os utilizadores selecionados tenham diferentes definições de política, pode clicar em **Criar uma nova política** para adicionar uma nova política.

1. Desatuale a data e a hora que pretende que a atribuição dos utilizadores selecionados comece e termine. Se não for fornecida uma data de fim, serão utilizadas as definições do ciclo de vida da apólice.

1. Opcionalmente, forneça uma justificação para a sua atribuição direta para a manutenção de registos.

1. Clique **em Adicionar** para atribuir diretamente os utilizadores selecionados ao pacote de acesso.

    Após alguns momentos, clique em **Refresh** para ver os utilizadores na lista de Atribuições.

### <a name="directly-assigning-users-programmatically"></a>Atribuir diretamente os utilizadores programaticamente

Também pode atribuir diretamente um utilizador a um pacote de acesso utilizando o Microsoft Graph.  Um utilizador numa função adequada com uma aplicação que tenha a permissão delegada `EntitlementManagement.ReadWrite.All` pode ligar para a API para criar um [AccessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true).

## <a name="remove-an-assignment"></a>Remover uma atribuição

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo, gestor de pacotes de acesso ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. No menu esquerdo, clique em **Atribuições**.
 
1. Clique na caixa de verificação ao lado do utilizador cuja atribuição pretende remover da embalagem de acesso. 

1. Clique no botão **Remover** perto da parte superior do painel esquerdo. 
 
    ![Atribuições - Remova o utilizador do pacote de acesso](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Aparecerá uma notificação informando-o de que a atribuição foi removida. 

## <a name="next-steps"></a>Passos seguintes

- [Alterar pedido e configurações para um pacote de acesso](entitlement-management-access-package-request-policy.md)
- [Ver relatórios e registos](entitlement-management-reports.md)