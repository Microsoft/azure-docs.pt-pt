---
title: Criar um novo pacote de acesso na gestão de direitos - Azure AD
description: Saiba como criar um novo pacote de recursos de acesso que pretende partilhar na gestão de direitos do Azure Ative Directory.
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
ms.openlocfilehash: e3df08272b352ee789c9879b1118105c435cffbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011090"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Criar um novo pacote de acesso na gestão de direitos AD da Azure

Um pacote de acesso permite-lhe fazer uma configuração única de recursos e políticas que administra automaticamente o acesso à vida útil do pacote de acesso. Este artigo descreve como criar um novo pacote de acesso.

## <a name="overview"></a>Descrição Geral

Todos os pacotes de acesso devem ser colocados num recipiente chamado catálogo. Um catálogo define os recursos que pode adicionar ao seu pacote de acesso. Se não especificar um catálogo, o seu pacote de acesso será colocado no catálogo geral. Atualmente, não é possível mover um pacote de acesso existente para um catálogo diferente.

Se você é um gestor de pacotes de acesso, você não pode adicionar recursos que você possui a um catálogo. Está restrito a utilizar os recursos disponíveis no catálogo. Se precisar de adicionar recursos a um catálogo, pode perguntar ao proprietário do catálogo.

Todos os pacotes de acesso devem ter pelo menos uma política. As políticas especificam quem pode solicitar o pacote de acesso e também a aprovação e as definições do ciclo de vida. Quando criar um novo pacote de acesso, pode criar uma política inicial para os utilizadores no seu diretório, para utilizadores que não estão no seu diretório, apenas para atribuições diretas de administrador, ou pode optar por criar a política mais tarde.

![Criar um pacote de acesso](./media/entitlement-management-access-package-create/access-package-create.png)

Aqui estão os passos de alto nível para criar um novo pacote de acesso.

1. Na Governação de Identidade, inicie o processo para criar um novo pacote de acesso.

1. Selecione o catálogo em que pretende criar o pacote de acesso.

1. Adicione recursos do catálogo ao seu pacote de acesso.

1. Atribua funções de recursos para cada recurso.

1. Especifique os utilizadores que podem solicitar o acesso.

1. Especifique quaisquer definições de aprovação.

1. Especifique as definições do ciclo de vida.

## <a name="start-new-access-package"></a>Inicie novo pacote de acesso

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso**.

1. Clique **em Novo pacote de acesso**.
   
    ![Gestão de direitos no portal Azure](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Noções básicas

No **separador Básicos,** você dá ao pacote de acesso um nome e especifica qual catálogo para criar o pacote de acesso dentro

1. Introduza o nome e a descrição do pacote de acesso. Os utilizadores verão esta informação quando apresentarem um pedido de pacote de acesso.

1. Na lista de drop-down do **Catálogo,** selecione o catálogo em que pretende criar o pacote de acesso. Por exemplo, você pode ter um proprietário de catálogo que gere todos os recursos de marketing que podem ser solicitados. Neste caso, pode selecionar o catálogo de marketing.

    Só verá catálogos onde tem permissão para criar pacotes de acesso. Para criar um pacote de acesso num catálogo existente, tem de ser administrador global ou administrador do Utilizador, ou deve ser proprietário de catálogo ou gestor de pacotes de acesso nesse catálogo.

    ![Pacote de acesso - Básicos](./media/entitlement-management-access-package-create/basics.png)

    Se é administrador global, administrador de utilizador ou criador de catálogos e gostaria de criar o seu pacote de acesso num novo catálogo que não está listado, clique em **Criar novo catálogo**. Introduza o nome e descrição do Catálogo e, em seguida, clique em **Criar**.

    O pacote de acesso que está a criar e quaisquer recursos incluídos nele serão adicionados ao novo catálogo. Também pode adicionar mais proprietários de catálogos mais tarde.

1. Clique em **Seguinte**.

## <a name="resource-roles"></a>Funções de recursos

No separador **Funções de Recursos,** seleciona os recursos a incluir no pacote de acesso. Os utilizadores que solicitarem e receberem o pacote de acesso receberão todas as funções de recursos no pacote de acesso.

1. Clique no tipo de recurso que pretende adicionar **(Grupos e Equipas,** **Aplicações** ou **sites SharePoint).**

1. No painel Select que aparece, selecione um ou mais recursos da lista.

    ![Pacote de acesso - Funções de recursos](./media/entitlement-management-access-package-create/resource-roles.png)

    Se estiver a criar o pacote de acesso no catálogo geral ou num novo catálogo, poderá escolher qualquer recurso do diretório que possui. Deve ser pelo menos um administrador global, um administrador do utilizador ou um criador de catálogos.

    Se estiver a criar o pacote de acesso num catálogo existente, pode selecionar qualquer recurso que já esteja no catálogo sem o possuir.

    Se é administrador global, administrador do Utilizador ou proprietário de catálogo, tem a opção adicional de selecionar recursos que possui que ainda não estão no catálogo. Se selecionar recursos que não estão atualmente no catálogo selecionado, estes recursos também serão adicionados ao catálogo para outros administradores de catálogos para construir pacotes de acesso com. Para ver todos os recursos que podem ser adicionados ao catálogo, consulte a caixa de verificação **ver todas as** caixas de verificação na parte superior do painel Select. Se pretender apenas selecionar recursos que se encontram atualmente no catálogo selecionado, deixe a caixa de verificação **Ver tudo** desmarcado (estado predefinido).

1. Uma vez selecionados os recursos, na lista **De Função,** selecione a função que pretende que os utilizadores sejam atribuídos para o recurso.

    ![Pacote de acesso - Seleção de funções de recursos](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Clique em **Seguinte**.

>[!NOTE]
>Pode adicionar grupos dinâmicos a um catálogo e a um pacote de acesso. No entanto, poderá selecionar apenas a função Proprietário ao gerir um recurso de grupo dinâmico num pacote de acesso.

## <a name="requests"></a>Pedidos

No separador **Pedidos,** cria a primeira política para especificar quem pode solicitar o pacote de acesso e também as definições de aprovação. Mais tarde, pode criar mais políticas de pedido para permitir que grupos adicionais de utilizadores solicitem o pacote de acesso com as suas próprias definições de aprovação.

![Pacote de acesso - Separador de Pedidos](./media/entitlement-management-access-package-create/requests.png)

Dependendo de quem pretender solicitar este pacote de acesso, execute os passos numa das seguintes secções.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Rever + criar

No **separador 'Rever +' criar,** pode rever as suas definições e verificar eventuais erros de validação.

1. Reveja as definições do pacote de acesso

    ![Pacote de acesso - Ativar a definição de políticas](./media/entitlement-management-access-package-create/review-create.png)

1. Clique em **Criar** para criar o pacote de acesso.

    O novo pacote de acesso aparece na lista de pacotes de acesso.

## <a name="creating-an-access-package-programmatically"></a>Criar um pacote de acesso programáticamente

Também pode criar um pacote de acesso utilizando o Microsoft Graph.  Um utilizador numa função apropriada com uma aplicação que tenha a permissão delegada `EntitlementManagement.ReadWrite.All` pode chamar a API para

1. [Enuprote os acessosPackageResources no catálogo](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta) e [crie um AccessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta) para quaisquer recursos que ainda não estejam no catálogo.
1. [Listar os acessosPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) de cada accessPackageResource num AccessPackageCatalog. Esta lista de funções será então usada para selecionar uma função, quando posteriormente criar um AccessPackageResourceRoleScope.
1. [Criar um pacote de acesso.](/graph/tutorial-access-package-api?view=graph-rest-beta)
1. [Criar um accessPackageAssignmentPolicy](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta).
1. [Crie um AccessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) para cada função de recurso necessária no pacote de acesso.

## <a name="next-steps"></a>Passos seguintes

- [Partilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md)
- [Alterar funções de recursos para um pacote de acesso](entitlement-management-access-package-resources.md)
- [Atribuir diretamente um utilizador ao pacote de acesso](entitlement-management-access-package-assignments.md)
- [Crie uma revisão de acesso para um pacote de acesso](entitlement-management-access-reviews-create.md)
