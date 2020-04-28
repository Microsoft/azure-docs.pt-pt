---
title: Criar um novo pacote de acesso na gestão de direitos - Azure AD
description: Saiba como criar um novo pacote de recursos que pretende partilhar na gestão de direitos do Diretório Ativo Azure.
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
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79262012"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Criar um novo pacote de acesso na gestão de direitos da Azure AD

Um pacote de acesso permite-lhe fazer uma configuração única de recursos e políticas que administra automaticamente o acesso para a vida útil do pacote de acesso. Este artigo descreve como criar um novo pacote de acesso.

## <a name="overview"></a>Descrição geral

Todos os pacotes de acesso devem ser colocados num contentor chamado catálogo. Um catálogo define quais os recursos que pode adicionar ao seu pacote de acesso. Se não especificar um catálogo, o seu pacote de acesso será colocado no catálogo Geral. Atualmente, não é possível mover um pacote de acesso existente para um catálogo diferente.

Se é gestor de pacotes de acesso, não pode adicionar recursos que possui a um catálogo. Está restrito a utilizar os recursos disponíveis no catálogo. Se precisar de adicionar recursos a um catálogo, pode perguntar ao proprietário do catálogo.

Todos os pacotes de acesso devem ter pelo menos uma política. As políticas especificam quem pode solicitar o pacote de acesso e também as definições de aprovação e ciclo de vida. Ao criar um novo pacote de acesso, pode criar uma política inicial para os utilizadores no seu diretório, para utilizadores que não estão no seu diretório, apenas para atribuições diretas de administrador, ou pode optar por criar a apólice mais tarde.

![Criar um pacote de acesso](./media/entitlement-management-access-package-create/access-package-create.png)

Aqui estão os passos de alto nível para criar um novo pacote de acesso.

1. Na Governação da Identidade, inicie o processo para criar um novo pacote de acesso.

1. Selecione o catálogo em que pretende criar o pacote de acesso.

1. Adicione recursos do catálogo ao seu pacote de acesso.

1. Atribuir funções de recursos para cada recurso.

1. Especifique os utilizadores que podem solicitar acesso.

1. Especifique quaisquer definições de aprovação.

1. Especifique as definições do ciclo de vida.

## <a name="start-new-access-package"></a>Iniciar novo pacote de acesso

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Clique em **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso**.

1. Clique em **Novo pacote de acesso**.
   
    ![Gestão de direitos no portal Azure](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Noções básicas

No separador **Basics,** dá ao pacote de acesso um nome e especifica em que catálogo criar o pacote de acesso.

1. Introduza o nome e descrição do programa de acesso. Os utilizadores verão esta informação quando submeterem um pedido para o pacote de acesso.

1. Na lista de entrega do **Catálogo,** selecione o catálogo em que pretende criar o pacote de acesso. Por exemplo, você pode ter um proprietário de catálogo que gere todos os recursos de marketing que podem ser solicitados. Neste caso, pode selecionar o catálogo de marketing.

    Só verá catálogos em que tem permissão para criar pacotes de acesso. Para criar um pacote de acesso num catálogo existente, deve ser administrador global ou administrador de utilizador, ou deve ser proprietário de catálogo ou gestor de pacotes de acesso nesse catálogo.

    ![Pacote de acesso - Básicos](./media/entitlement-management-access-package-create/basics.png)

    Se for um administrador global, um administrador de utilizador ou criador de catálogos e gostaria de criar o seu pacote de acesso num novo catálogo que não esteja listado, clique em **Criar um novo catálogo**. Introduza o nome e descrição do Catálogo e, em seguida, clique em **Criar**.

    O pacote de acesso que está a criar e todos os recursos incluídos serão adicionados ao novo catálogo. Também pode adicionar mais tarde aos proprietários de catálogos adicionais.

1. Clique em **Seguinte**.

## <a name="resource-roles"></a>Funções de recursos

No separador **funções De Recurso,** selecione os recursos a incluir no pacote de acesso. Os utilizadores que solicitarem e receberem o pacote de acesso receberão todas as funções de recurso no pacote de acesso.

1. Clique no tipo de recursos que pretende adicionar (**Grupos e Equipas,** **Aplicações**ou **sites SharePoint).**

1. No painel Select que aparece, selecione um ou mais recursos da lista.

    ![Pacote de acesso - Funções de recursos](./media/entitlement-management-access-package-create/resource-roles.png)

    Se estiver a criar o pacote de acesso no catálogo Geral ou num novo catálogo, poderá escolher qualquer recurso do diretório que possui. Deve ser pelo menos um administrador global, um administrador de utilizador ou criador de Catálogo.

    Se estiver a criar o pacote de acesso num catálogo existente, pode selecionar qualquer recurso que já esteja no catálogo sem o possuir.

    Se é administrador global, administrador de utilizador ou proprietário de catálogo, tem a opção adicional de selecionar recursos que possui que ainda não estão no catálogo. Se selecionar recursos que não estejam atualmente no catálogo selecionado, estes recursos também serão adicionados ao catálogo para outros administradores de catálogo saem para construir pacotes de acesso. Se pretender selecionar apenas recursos que se encontram atualmente no catálogo selecionado, verifique a caixa de verificação **Only see** na parte superior do painel Select.

1. Uma vez selecionados os recursos, na lista **De Função,** selecione a função que pretende que os utilizadores sejam atribuídos para o recurso.

    ![Pacote de acesso - Seleção de Funções de Recursos](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Clique em **Seguinte**.

## <a name="requests"></a>Pedidos

No separador **Pedidos,** cria a primeira política para especificar quem pode solicitar o pacote de acesso e também as definições de aprovação. Mais tarde, pode criar mais políticas de pedidos para permitir que grupos adicionais de utilizadores solicitem o pacote de acesso com as suas próprias definições de aprovação.

![Pacote de acesso - Separador de pedidos](./media/entitlement-management-access-package-create/requests.png)

Dependendo de quem pretende solicitar este pacote de acesso, execute os passos numa das seguintes secções.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Rever + criar

No **separador Review + criar,** pode rever as suas definições e verificar se há erros de validação.

1. Reveja as definições do pacote de acesso

    ![Pacote de acesso - Política- Ativar a definição de políticas](./media/entitlement-management-access-package-create/review-create.png)

1. Clique em **Criar** para criar o pacote de acesso.

    O novo pacote de acesso aparece na lista de pacotes de acesso.

## <a name="creating-an-access-package-programmatically"></a>Criar um pacote de acesso programática

Também pode criar um pacote de acesso utilizando o Microsoft Graph.  Um utilizador em uma função apropriada com `EntitlementManagement.ReadWrite.All` uma aplicação que tenha a permissão delegada pode chamar a API para

1. [Enumera rine os acessosPackageResources no catálogo](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta) e [crie um AccessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta) para quaisquer recursos que ainda não estejam no catálogo.
1. [Enumerar o acessoPackageResourceRoles](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta) de cada acessoPackageResource num accessPackageCatalog. Esta lista de funções será então utilizada para selecionar uma função, quando posteriormente criar um acesso PackageResourceRoleScope.
1. [Criar um pacote de acesso](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta).
1. [Criar uma Política de Atribuição de Pacotes](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta)de acesso.
1. [Crie um acessoPackageResourceRoleScope](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta) para cada função de recurso necessária no pacote de acesso.

## <a name="next-steps"></a>Passos seguintes

- [Partilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md)
- [Alterar as funções de recurso para um pacote de acesso](entitlement-management-access-package-resources.md)
- [Atribuir diretamente um utilizador ao pacote de acesso](entitlement-management-access-package-assignments.md)
