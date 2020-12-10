---
title: Colaborar com outros - LUIS
titleSuffix: Azure Cognitive Services
description: Um proprietário de aplicações pode adicionar contribuintes ao recurso de autoria. Estes colaboradores podem modificar o modelo, treinar e publicar a app.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/08/2020
ms.openlocfilehash: d4bde21dd13b562ffbb51b27ef083ee53685397a
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007976"
---
# <a name="add-contributors-to-your-app"></a>Adicione colaboradores à sua app

Um proprietário de aplicações pode adicionar contribuintes a apps. Estes colaboradores podem modificar o modelo, treinar e publicar a app. Depois de [migrar a](luis-migration-authoring.md) sua conta, _os contribuintes_ são geridos no portal Azure para o recurso de autoria, utilizando a página **de Controlo de Acesso (IAM).** Adicione um utilizador, utilizando o endereço de e-mail do colaborador e a _função de contribuinte._

## <a name="add-contributor-to-azure-authoring-resource"></a>Adicionar colaborador ao recurso de autoria Azure

Emigrou se a sua experiência de autoria LUIS está ligada a um recurso de Autoria na página **de recursos Manage-> Azure** no portal LUIS.

1. No portal Azure, encontre o recurso de autoria do Conhecimento Linguístico (LUIS). Tem o `LUIS.Authoring` tipo.
1. Na página de Controlo de **Acesso (IAM)** deste recurso, selecione **+Adicionar** e selecione **Adicionar a atribuição de função**.

    ![No portal Azure, adicione a atribuição de funções na autoria de recursos.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Na janela **de atribuição de funções Adicionar,** selecione o **Papel** do Contribuinte. Na opção **De atribuir acesso à** opção, selecione **utilizador, grupo ou principal de serviço Azure.** Na opção **Select,** insira o endereço de e-mail do utilizador. Se o utilizador for conhecido por mais de 1 endereço de e-mail para o mesmo domínio, certifique-se de que introduz a conta de e-mail _primária._

    ![Adicione o e-mail do utilizador ao papel de contribuinte para a Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Quando o e-mail do utilizador for encontrado, selecione a conta e selecione **Guardar**.

    Se tiver problemas com esta atribuição de funções, reveja [as atribuições de funções da Azure](../../role-based-access-control/role-assignments-portal.md) e [a resolução de problemas do controlo de acesso da Azure](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="view-the-app-as-a-contributor"></a>Ver a app como um contribuinte

Depois de ter sido adicionado como contribuinte, [inscreva-se no portal LUIS](sign-in-luis-portal.md).

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>Utilizadores com vários e-mails

Se adicionar colaboradores a uma aplicação LUIS, está a especificar o endereço de e-mail exato. Enquanto o Azure Ative Directory (Azure AD) permite que um único utilizador tenha mais do que uma conta de e-mail utilizada intercambiavelmente, a LUIS exige que o utilizador assine com o endereço de e-mail especificado ao adicionar o contribuinte.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Recursos do Diretório Ativo Azure

Se utilizar [o Azure Ative Directory](../../active-directory/index.yml) (Azure AD) na sua organização, a Language Understanding (LUIS) necessita de permissão para a informação sobre o acesso dos seus utilizadores quando pretender utilizar o LUIS. Os recursos que o LUIS exige são mínimos.

Você vê a descrição detalhada quando tenta se inscrever com uma conta que tem consentimento administrativo ou não requer consentimento administrativo, como o consentimento do administrador:

* Permite-lhe iniciar sessão na app com a sua conta organizacional e deixar a aplicação ler o seu perfil. Também permite que a app leia informações básicas da empresa. Isto dá permissão ao LUIS para ler dados básicos de perfil, tais como ID do utilizador, e-mail, nome
* Permite que a aplicação veja e atualize os seus dados, mesmo quando não está atualmente a utilizar a app. A permissão é necessária para refrescar o sinal de acesso do utilizador.


### <a name="azure-active-directory-tenant-user"></a>Utilizador inquilino do Azure Ative Directory

A LUIS utiliza o fluxo de consentimento padrão do Azure Ative (Azure AD).

O administrador inquilino deve trabalhar diretamente com o utilizador que precisa de acesso concedido para usar LUIS no AD AZure.

* Primeiro, o utilizador assina no LUIS e vê o diálogo pop-up a necessitar de aprovação de administração. O utilizador contacta o administrador do arrendatário antes de continuar.
* Segundo, o administrador do inquilino assina no LUIS, e vê um diálogo pop-up de fluxo de consentimento. Este é o diálogo que o administrador precisa para dar permissão ao utilizador. Uma vez que o administrador aceite a permissão, o utilizador pode continuar com o LUIS. Se o administrador inquilino não assinar com LUIS, o administrador pode aceder ao [consentimento](https://account.activedirectory.windowsazure.com/r#/applications) de LUIS, mostrado na imagem seguinte. Note que a lista é filtrada para itens que incluem o nome `LUIS` .

![Azure permissão de diretório ativo por site de aplicações](./media/luis-how-to-collaborate/tenant-permissions.png)

Se o administrador inquilino apenas quer que certos utilizadores utilizem o LUIS, existem algumas soluções possíveis:
* Dando o "consentimento administrativo" (consentimento a todos os utilizadores do AD Azure), mas depois definido como "Sim" a "atribuição de utilizador necessária" nas Propriedades da Aplicação da Empresa e, finalmente, atribuir/adicionar apenas os utilizadores desejados à Aplicação. Com este método, o Administrador ainda está a fornecer "consentimento administrativo" à App, no entanto, é possível controlar os utilizadores que possam aceder à sua sê-lo.
* Uma segunda solução, é utilizar a API de gestão de [identidade AZure e gestão de acessos no Microsoft Graph](/graph/azuread-identity-access-management-concept-overview) para fornecer consentimento a cada utilizador específico.

Saiba mais sobre os utilizadores de diretórios ativos da Azure e o consentimento:
* [Restringir a sua aplicação](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a um conjunto de utilizadores

## <a name="next-steps"></a>Passos seguintes

* Aprenda [a usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida da sua aplicação.
* Compreenda os conceitos, incluindo o [recurso de autoria](luis-how-to-azure-subscription.md#authoring-key) e [os contribuintes](luis-how-to-azure-subscription.md#contributions-from-other-authors) nesse recurso.
* Saiba [como criar](luis-how-to-azure-subscription.md) recursos de autoria e tempo de execução
* Migrar para o novo [recurso de autoria](luis-migration-authoring.md)