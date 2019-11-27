---
title: Colaborar com outras pessoas – LUIS
titleSuffix: Azure Cognitive Services
description: Um proprietário de aplicativo pode adicionar colaboradores ao recurso de criação. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 96aa7517a3418ab30b0b6a1736eea950ecf4a731
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533793"
---
# <a name="add-contributors-to-your-app"></a>Adicionar colaboradores ao seu aplicativo

Um proprietário de aplicativo pode adicionar colaboradores a aplicativos. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Adicionar colaborador ao recurso de criação do Azure

O procedimento a seguir é para todos os usuários que **migraram** para usar o recurso de criação do Azure.

Você migrou se sua experiência de criação do LUIS está vinculada a um recurso de criação na página **gerenciar recursos do Azure->** no portal do Luis.

1. Na portal do Azure, localize o recurso de criação de Reconhecimento vocal (LUIS). Ele tem o tipo `LUIS.Authoring`.
1. Na página controle de **acesso deste recurso (iam)** , selecione **+ Adicionar** e selecione **Adicionar atribuição de função**.

    ![Em portal do Azure, adicione atribuição de função ao recurso de criação.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Na janela **Adicionar atribuição de função** , selecione a **função** de colaborador. Na opção **atribuir acesso a** , selecione **usuário, grupo ou entidade de serviço do Azure ad**. Na opção **selecionar** , insira o endereço de email do usuário. Se o usuário for conhecido por mais de um endereço de email para o mesmo domínio, certifique-se de inserir a conta de email _principal_ .

    ![Adicionar email do usuário à função colaborador do Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Quando o email do usuário for encontrado, selecione a conta e selecione **salvar**. 

    Se você tiver problemas com essa atribuição de função, examine as [atribuições de função do Azure](../../role-based-access-control/role-assignments-portal.md) e [solução de problemas do controle de acesso do Azure](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Adicionar colaborador ao aplicativo LUIS

O procedimento a seguir é para todos os usuários que **não migraram** para usar o recurso de criação do Azure.

Você não migrou se sua experiência de criação do LUIS não estiver vinculada a um recurso de criação na página **gerenciar recursos do Azure->** no portal do Luis.

Um aplicativo tem um único autor, o proprietário, mas pode ter muitos colaboradores. Para permitir que colaboradores editem seu aplicativo LUIS, você deve adicionar o email que eles usam para acessar o portal do LUIS para a lista de colaboradores. Depois que eles forem adicionados, o aplicativo será exibido em seu portal do LUIS.

1. Selecione **gerenciar** no menu superior direito e, em seguida, selecione **colaboradores** no menu à esquerda.

1. Selecione **Adicionar colaborador** na barra de ferramentas.

1. Insira o endereço de email que o colaborador usa para entrar no portal do LUIS.

    ![Adicionar endereço de email do colaborador](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Usuários com vários emails 

Se você adicionar colaboradores/colaboradores a um aplicativo LUIS, você estará especificando o endereço de email exato. Embora Azure Active Directory (Azure AD) permita que um único usuário tenha mais de uma conta de email usada de forma intercambiável, o LUIS exige que o usuário entre com o endereço de email especificado ao adicionar o colaborador/colaborador.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory recursos

Se você usar [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) em sua organização, reconhecimento vocal (Luis) precisará de permissão para as informações sobre o acesso dos usuários quando quiserem usar o Luis. Os recursos que o LUIS exige são mínimos. 

Você verá a descrição detalhada ao tentar se inscrever com uma conta que tenha consentimento de administrador ou que não exija o consentimento do administrador, como o consentimento dos administradores:

* Permite que você entre no aplicativo com sua conta institucional e deixe que o aplicativo Leia seu perfil. Ele também permite que o aplicativo Leia informações básicas da empresa. Isso concede ao LUIS permissão para ler dados básicos de perfil, como ID de usuário, email, nome
* Permite que o aplicativo Veja e atualize seus dados, mesmo quando você não estiver usando o aplicativo no momento. A permissão é necessária para atualizar o token de acesso do usuário.


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory usuário de locatário

O LUIS usa o fluxo de consentimento do Azure Active Directory (AD do Azure) padrão. 

O administrador de locatários deve trabalhar diretamente com o usuário que precisa de acesso concedido para usar o LUIS no Azure AD. 

* Primeiro, o usuário entra no LUIS e vê a caixa de diálogo pop-up que precisa de aprovação do administrador. O usuário entra em contato com o administrador de locatários antes de continuar. 
* Em segundo lugar, o administrador de locatários entra no LUIS e vê uma caixa de diálogo pop-up do fluxo de autorização. Essa é a caixa de diálogo que o administrador precisa para conceder permissão para o usuário. Depois que o administrador aceita a permissão, o usuário é capaz de continuar com o LUIS. Se o administrador de locatários não entrar no LUIS, o administrador poderá acessar o [consentimento](https://account.activedirectory.windowsazure.com/r#/applications) do Luis, mostrado na captura de tela a seguir. Observe que a lista é filtrada para itens que incluem o nome `LUIS`.

![Permissão do Azure Active Directory por site do aplicativo](./media/luis-how-to-collaborate/tenant-permissions.png)

Se o administrador de locatários quiser que apenas determinados usuários usem LUIS, haverá algumas soluções possíveis:
* Fornecendo o "consentimento do administrador" (consentimento para todos os usuários do Azure AD), mas, em seguida, defina como "Sim" a "atribuição de usuário necessária" em Propriedades do aplicativo empresarial e, por fim, atribua/adicione somente os usuários desejados ao aplicativo. Com esse método, o administrador ainda está fornecendo "consentimento do administrador" para o aplicativo, no entanto, é possível controlar os usuários que podem acessá-lo.
* Uma segunda solução é usar a API de [Gerenciamento de acesso e identidade do Azure AD no Microsoft Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) para fornecer consentimento para cada usuário específico. 

Saiba mais sobre os usuários e o consentimento do Azure Active Directory: 
* [Restringir seu aplicativo](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a um conjunto de usuários

## <a name="next-steps"></a>Passos seguintes

* Saiba [como usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida do aplicativo.
* Entenda os conceitos, incluindo o [recurso de criação](luis-concept-keys.md#authoring-key) e os [colaboradores](luis-concept-keys.md#contributions-from-other-authors) nesse recurso.
* Saiba [como criar](luis-how-to-azure-subscription.md) recursos de criação e tempo de execução
* Migrar para o novo [recurso de criação](luis-migration-authoring.md) 
