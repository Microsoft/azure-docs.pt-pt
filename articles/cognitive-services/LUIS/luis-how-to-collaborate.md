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
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: e69fea4d57aaf2a0b7d3615b1eecbf99a6b13ab8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256824"
---
# <a name="add-contributors-to-your-app"></a>Adicionar colaboradores ao seu aplicativo

Um proprietário de aplicativo pode adicionar colaboradores a aplicativos. Estes colaboradores podem modificar o modelo, formar e publicar a aplicação. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Adicionar colaborador ao recurso de criação do Azure

O procedimento a seguir é para todos os usuários que migraram para usar o recurso de criação do Azure.

Você migrou se sua experiência de criação do LUIS está vinculada a um recurso de criação na página **gerenciar recursos do Azure->** no portal do Luis.

1. Na portal do Azure, localize o recurso de criação de Reconhecimento vocal (LUIS). Ele tem o tipo `LUIS.Authoring`.
1. Na página controle de **acesso deste recurso (iam)** , selecione **+ Adicionar** e selecione **Adicionar atribuição de função**.

    ![Em portal do Azure, adicione atribuição de função ao recurso de criação.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Na janela **Adicionar atribuição de função** , selecione a **função** de colaborador. Na opção **atribuir acesso a** , selecione **usuário, grupo ou entidade de serviço do Azure ad**. Na opção **selecionar** , insira o endereço de email do usuário. Se o usuário for conhecido por mais de um endereço de email para o mesmo domínio, certifique-se de inserir a conta de email _principal_ .

    ![Adicionar email do usuário à função colaborador do Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Quando o email do usuário for encontrado, selecione a conta e selecione **salvar**. 

    Se você tiver problemas com essa atribuição de função, examine as atribuições de [função do Azure](../../role-based-access-control/role-assignments-portal.md) e [solução de problemas do controle de acesso do Azure](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Adicionar colaborador ao aplicativo LUIS

O procedimento a seguir é para todos os usuários que **não migraram** para usar o recurso de criação do Azure.

Você não migrou se sua experiência de criação do LUIS não estiver vinculada a um recurso de criação na página **gerenciar recursos do Azure->** no portal do Luis.

Uma aplicação tem um autor único, o proprietário, mas pode ter muitos funcionários. Para permitir que os funcionários para editar a sua aplicação LUIS, tem de adicionar o e-mail que utilizam para aceder ao portal de LUIS para a lista de colaboradores. Depois que serem adicionados, a aplicação é apresentada no seu portal de LUIS.

1. Selecione **Manage** a partir do menu superior direito, em seguida, selecione **colaboradores** no menu à esquerda.

1. Selecione **adicionar acesso do funcionário** da barra de ferramentas.

    [![Adicionar acesso do funcionário](./media/luis-how-to-collaborate/add-collaborator.png "adicionar acesso do funcionário")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

1. Introduza o endereço de e-mail, que o acesso do funcionário utiliza para iniciar sessão portal do LUIS.

    ![Adicionar endereço de e-mail do colaborador](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Usuários com vários emails 

Se você adicionar colaboradores/colaboradores a um aplicativo LUIS, você estará especificando o endereço de email exato. Embora Azure Active Directory (Azure AD) permita que um único usuário tenha mais de uma conta de email usada de forma intercambiável, o LUIS exige que o usuário entre com o endereço de email especificado ao adicionar o colaborador/colaborador.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Recursos do Azure Active Directory

Se você usar [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) em sua organização, reconhecimento vocal (Luis) precisará de permissão para as informações sobre o acesso dos usuários quando quiserem usar o Luis. Os recursos de que necessita de LUIS são mínimos. 

Veja a descrição detalhada quando tentar inscrever-se com uma conta que tem o consentimento de administrador ou não necessita de consentimento de administrador, como o consentimento de administrador:

* Permite-lhe iniciar sessão na aplicação com a sua conta organizacional e permitir que a aplicação leia o seu perfil. Também permite que a aplicação Leia informações básicas da empresa. Isso concede ao LUIS permissão para ler dados básicos de perfil, como ID de usuário, email, nome
* Permite que a aplicação ver e atualizar os seus dados, mesmo quando não está a utilizar a aplicação. A permissão é necessária para atualizar o token de acesso do usuário.


### <a name="azure-active-directory-tenant-user"></a>Utilizador de inquilino do Azure Active Directory

LUIS usa o fluxo de consentimento do padrão do Azure Active Directory (Azure AD). 

O administrador de inquilinos deve trabalhar diretamente com o utilizador que precisa de acesso concedido a utilizar o LUIS no Azure AD. 

* Em primeiro lugar, o utilizador inicia sessão LUIS e vê a caixa de diálogo pop-up que precisam de aprovação de administrador. O usuário entra em contacto com o administrador de inquilino antes de continuar. 
* Em segundo lugar, o administrador de inquilino iniciar sessão numa LUIS e vê um diálogo de pop-up de fluxo de consentimento. Esta é a caixa de diálogo que o administrador tem de conceder permissão para o utilizador. Assim que o administrador de aceita a permissão, o utilizador é continuar com os LUIS. Se o administrador de locatários não entrar no LUIS, o administrador poderá acessar o [consentimento](https://account.activedirectory.windowsazure.com/r#/applications) do Luis, mostrado na captura de tela a seguir. Observe que a lista é filtrada para itens que incluem `LUIS`o nome.

![Permissão do Azure active directory ao Web site da aplicação](./media/luis-how-to-collaborate/tenant-permissions.png)

Se o administrador de locatários quiser que apenas determinados usuários usem LUIS, haverá algumas soluções possíveis:
* Fornecendo o "consentimento do administrador" (consentimento para todos os usuários do Azure AD), mas, em seguida, defina como "Sim" a "atribuição de usuário necessária" em Propriedades do aplicativo empresarial e, por fim, atribua/adicione somente os usuários desejados ao aplicativo. Com esse método, o administrador ainda está fornecendo "consentimento do administrador" para o aplicativo, no entanto, é possível controlar os usuários que podem acessá-lo.
* Uma segunda solução é usar o [Azure AD API do Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) para fornecer consentimento para cada usuário específico. 

Saiba mais sobre os usuários e o consentimento do Azure Active Directory: 
* [Restringir seu aplicativo](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a um conjunto de usuários

## <a name="next-steps"></a>Passos Seguintes

* Saiba [como usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida do aplicativo.
* Entenda os conceitos, incluindo o [recurso de criação](/luis-concept-keys.md#authoring-key) e os [colaboradores](luis-concept-keys.md#contributions-from-other-authors) nesse recurso.
* Saiba [como criar](luis-how-to-azure-subscription.md) recursos de criação e tempo de execução
* Migrar para o novo [recurso de criação](luis-migration-authoring.md) 
