---
title: Colaborar com os outros - LUIS
titleSuffix: Azure Cognitive Services
description: O proprietário de uma aplicação pode adicionar colaboradores ao recurso de autoria. Estes colaboradores podem modificar o modelo, treinar e publicar a app.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390095"
---
# <a name="add-contributors-to-your-app"></a>Adicione colaboradores à sua app

Um proprietário de aplicativos pode adicionar colaboradores a apps. Estes colaboradores podem modificar o modelo, formar e publicar a aplicação. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Adicionar colaborador ao recurso de autoria azure

O seguinte procedimento é para todos os utilizadores que **migraram** para utilizar o recurso de autoria Azure.

Emigrou se a sua experiência de autoria da LUIS estiver ligada a um recurso de autoria na página **de recursos Manage &> Azure** no portal LUIS.

1. No portal Azure, encontre o recurso de autoria da Compreensão linguística (LUIS). Tem o tipo `LUIS.Authoring`.
1. Na página de Controlo de **Acesso (IAM)** deste recurso, selecione **+Adicionar** e, em seguida, selecione adicionar a atribuição de **funções**.

    ![No portal Azure, adicione a atribuição de funções no recurso de autoria.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Na janela de atribuição de **funções Adicionar,** selecione o **Papel** de Contribuinte. No **acesso atribuído à** opção, selecione utilizador, grupo ou diretor de **serviço Azure AD**. Na opção **Select,** introduza o endereço de e-mail do utilizador. Se o utilizador for conhecido por mais de 1 endereço de e-mail para o mesmo domínio, certifique-se de introduzir a conta de e-mail _primária._

    ![Adicione o e-mail do utilizador ao papel de contribuinte para a AD Azure](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Quando o e-mail do utilizador for encontrado, selecione a conta e selecione **Guardar**. 

    Se tiver problemas com esta atribuição de funções, reveja as atribuições de [papel do Azure](../../role-based-access-control/role-assignments-portal.md) e o controlo de acesso do [Azure.](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments)

## <a name="add-collaborator-to-luis-app"></a>Adicionar colaborador à app LUIS

O seguinte procedimento é para todos os utilizadores que **não migraram** para utilizar o recurso de autoria Azure.

Não emigrou se a sua experiência de autoria luis não estiver ligada a um recurso de autoria na página **de recursos Manage &> Azure** no portal LUIS.

Uma aplicação tem um autor único, o proprietário, mas pode ter muitos funcionários. Para permitir que os funcionários para editar a sua aplicação LUIS, tem de adicionar o e-mail que utilizam para aceder ao portal de LUIS para a lista de colaboradores. Depois que serem adicionados, a aplicação é apresentada no seu portal de LUIS.

1. Selecione **Gerir** a partir do menu superior direito e, em seguida, selecione **Colaboradores** no menu esquerdo.

1. **Selecione Adicionar Colaborador** a partir da barra de ferramentas.

1. Introduza o endereço de e-mail, que o acesso do funcionário utiliza para iniciar sessão portal do LUIS.

    ![Adicionar endereço de e-mail do colaborador](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Utilizadores com vários e-mails 

Se adicionar colaboradores/colaboradores a uma aplicação LUIS, está a especificar o endereço de e-mail exato. Enquanto o Azure Ative Directory (Azure AD) permite que um único utilizador tenha mais do que uma conta de e-mail utilizada intercambiavelmente, a LUIS exige que o utilizador assine com o endereço de e-mail especificado ao adicionar o colaborador/colaborador.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Recursos do Azure Active Directory

Se utilizar o [Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) na sua organização, o Language Understanding (LUIS) necessita de autorização para obter informações sobre o acesso dos seus utilizadores quando pretendem utilizar o LUIS. Os recursos de que necessita de LUIS são mínimos. 

Veja a descrição detalhada quando tentar inscrever-se com uma conta que tem o consentimento de administrador ou não necessita de consentimento de administrador, como o consentimento de administrador:

* Permite-lhe iniciar sessão na aplicação com a sua conta organizacional e permitir que a aplicação leia o seu perfil. Também permite que a aplicação Leia informações básicas da empresa. Isto dá permissão ao LUIS para ler dados básicos de perfil, tais como ID do utilizador, e-mail, nome
* Permite que a aplicação ver e atualizar os seus dados, mesmo quando não está a utilizar a aplicação. A permissão é necessária para refrescar o sinal de acesso do utilizador.


### <a name="azure-active-directory-tenant-user"></a>Utilizador de inquilino do Azure Active Directory

LUIS usa o fluxo de consentimento do padrão do Azure Active Directory (Azure AD). 

O administrador de inquilinos deve trabalhar diretamente com o utilizador que precisa de acesso concedido a utilizar o LUIS no Azure AD. 

* Em primeiro lugar, o utilizador inicia sessão LUIS e vê a caixa de diálogo pop-up que precisam de aprovação de administrador. O usuário entra em contacto com o administrador de inquilino antes de continuar. 
* Em segundo lugar, o administrador de inquilino iniciar sessão numa LUIS e vê um diálogo de pop-up de fluxo de consentimento. Esta é a caixa de diálogo que o administrador tem de conceder permissão para o utilizador. Assim que o administrador de aceita a permissão, o utilizador é continuar com os LUIS. Se o administrador inquilino não assinar a LUIS, o administrador pode aceder ao [consentimento](https://account.activedirectory.windowsazure.com/r#/applications) da LUIS, mostrado na seguinte imagem. Note que a lista é filtrada para itens que incluem o nome `LUIS`.

![Permissão do Azure active directory ao Web site da aplicação](./media/luis-how-to-collaborate/tenant-permissions.png)

Se o administrador inquilino apenas quer que certos utilizadores utilizem o LUIS, existem algumas soluções possíveis:
* Dando o "consentimento administrativo" (consentimento a todos os utilizadores da AD Azure), mas, em seguida, definir para "Sim" a "atribuição de utilizador necessária" no âmbito das Propriedades da Aplicação Empresarial e, finalmente, atribuir/adicionar apenas os utilizadores procurados à Aplicação. Com este método, o Administrador ainda está a fornecer "consentimento administrativo" à App, no entanto, é possível controlar os utilizadores que podem aceder à sua acesso.
* Uma segunda solução é utilizando a [identidade Azure AD e a API de gestão](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) de acesso no Microsoft Graph para dar consentimento a cada utilizador específico. 

Saiba mais sobre os utilizadores de diretórios ativos do Azure e o consentimento: 
* [Restrinja a sua aplicação](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a um conjunto de utilizadores

## <a name="next-steps"></a>Passos Seguintes

* Aprenda [a usar versões](luis-how-to-manage-versions.md) para controlar o seu ciclo de vida da aplicação.
* Compreenda os conceitos, incluindo o [recurso de autoria](luis-concept-keys.md#authoring-key) e os [contribuintes](luis-concept-keys.md#contributions-from-other-authors) desse recurso.
* Aprenda [a criar](luis-how-to-azure-subscription.md) recursos de autoria e tempo de funcionamento
* Migrar para o novo [recurso de autoria](luis-migration-authoring.md) 
