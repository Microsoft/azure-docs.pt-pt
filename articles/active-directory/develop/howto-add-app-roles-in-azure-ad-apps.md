---
title: Adicione as funções de aplicativo e obtenha-as a partir de um token ! Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como adicionar funções de aplicação a uma aplicação registada no Azure Ative Directory, atribuir utilizadores e grupos a estas funções e recebê-las na reivindicação de 'roles' no token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706018"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Como: Adicionar funções de aplicação na sua aplicação e recebê-las no token

O controlo de acesso baseado em funções (RBAC) é um mecanismo popular para impor a autorização em aplicações. Ao utilizar o RBAC, um administrador concede permissões a funções e não a utilizadores ou grupos individuais. O administrador pode então atribuir funções a diferentes utilizadores e grupos para controlar quem tem acesso a que conteúdo e funcionalidade.

Utilizando o RBAC com Funções de Aplicação e Reivindicações de Funções, os desenvolvedores podem impor de forma segura a autorização nas suas apps com menos esforço da sua parte.

Outra abordagem é utilizar grupos AD Ad E reivindicações de grupo, como mostrado na amostra de código [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) no GitHub. Os grupos AD Azure e as Funções de Aplicação não são mutuamente exclusivos; podem ser usados em conjunto para fornecer um controlo de acesso ainda mais fino.

## <a name="declare-roles-for-an-application"></a>Declarar funções para uma aplicação

As funções de candidatura são definidas no [portal Azure](https://portal.azure.com).  Quando um utilizador assina a aplicação, a Azure AD emite uma `roles` reclamação por cada papel que o utilizador tenha sido concedido individualmente ao utilizador e da sua filiação em grupo.  A atribuição de utilizadores e grupos a funções pode ser feita através da UI do portal, ou utilizando programáticamente [o Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

Para criar um papel de aplicação:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Directory +** no menu superior e, em seguida, escolha o inquilino do Azure Ative Directory que contém o registo da aplicação ao qual pretende adicionar uma função de app.
1. Procure e selecione **Azure Active Directory**.
1. Em **Manage**, selecione **registos de Aplicações**e, em seguida, selecione a aplicação em que pretende definir funções de aplicação.
1. Selecione **funções de App / Pré-visualizar**e, em seguida, selecionar **Criar o papel da aplicação**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Um painel de funções de aplicação de registo de aplicações no portal Azure":::
1. No painel de funções da **aplicação Create,** introduza as definições para o papel. A tabela que segue a imagem descreve cada definição e os seus parâmetros.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="As funções de aplicação de um registo de aplicações criam um painel de contexto no portal Azure":::

    | Campo | Descrição | Exemplo |
    |-------|-------------|---------|
    | **Nome a apresentar** | Mostrar o nome para o papel da aplicação que aparece nas experiências de consentimento de administração e atribuição de aplicações. Este valor pode conter espaços.  | `Survey Writer` |
    | **Tipos de membros permitidos** | Especifica se esta função da app pode ser atribuída a utilizadores, aplicações ou ambos. | `Users/Groups` |
    | **Valor** | Especifica o valor das funções alegando que a aplicação deve esperar no token. O valor deve corresponder exatamente à cadeia referenciada no código da aplicação. O valor não pode conter espaços. | `Survey.Create` |
    | **Descrição** | Uma descrição mais detalhada da função da aplicação exibida durante a atribuição de aplicações de administração e experiências de consentimento. | `Writers can create surveys.` |
    | **Deseja ativar esta função de aplicação?** | Se o papel da aplicação está ativado. Para eliminar uma função de aplicação, desmarcar esta caixa de verificação e aplicar a alteração antes de tentar a operação de eliminação. | *Marcado* |

1. Selecione **Aplicar** para guardar as alterações.

> [!NOTE]
> O número de funções que adiciona conta para limites definidos para um manifesto de aplicação. Para obter informações sobre estes limites, consulte a secção [Desproteturas](./reference-app-manifest.md#manifest-limits) da [aplicação Azure Ative Directory.](reference-app-manifest.md)

## <a name="assign-users-and-groups-to-roles"></a>Atribuir utilizadores e grupos a funções

Uma vez adicionadas funções de aplicação na sua aplicação, pode atribuir utilizadores e grupos às funções.

1. No **Azure Ative Directory** no portal Azure, selecione **aplicações Enterprise** no menu de navegação à esquerda.
1. Selecione **Todas as aplicações** para ver uma lista de todas as suas aplicações.

     Se a sua aplicação não aparecer na lista, utilize os filtros no topo da lista de **aplicações** para restringir a lista, ou desloque a lista para localizar a sua aplicação.

1. Selecione a aplicação na qual pretende atribuir utilizadores ou grupo de segurança a funções.
1. Em **Gerir**, selecione **Utilizadores e grupos**.
1. **Selecione Adicionar** ao utilizador para abrir o painel de atribuição **de adicionar.**
1. Selecione o seletor **de Utilizadores e grupos** a partir do painel **de atribuição de adicionar.**

     É apresentada uma lista de utilizadores e grupos de segurança. Pode pesquisar por um determinado utilizador ou grupo, bem como selecionar vários utilizadores e grupos que aparecem na lista.

1. Uma vez selecionados utilizadores e grupos, selecione o botão **Select** para proceder.
1. **Selecione Selecione Função** no painel **de atribuição adicionar.** Todos os papéis que definiu para a aplicação são apresentados.
1. Escolha uma função e selecione o botão **Select.**
1. Selecione o botão **Atribuir** para terminar a atribuição de utilizadores e grupos para a aplicação.
1. Confirme que os utilizadores e grupos adicionados constam da lista **de Utilizadores e grupos.**

## <a name="receive-roles-in-tokens"></a>Receber papéis em fichas

Quando os utilizadores atribuídos às várias funções da aplicação iniciarem o sessão na aplicação, os seus tokens terão as suas funções atribuídas na `roles` reclamação.

## <a name="web-api-application-permissions"></a>Permissões de aplicações da Web API

Pode definir funções de aplicações que **direcionem utilizadores/grupos,** **aplicações,** ou **ambas.** Quando seleciona **Aplicações** ou **Ambos,** a função da aplicação aparece como uma permissão de aplicação nas **permissões de API**de uma aplicação do cliente.

Para selecionar a permissão de aplicação num registo de aplicações de cliente depois de ter definido uma função que visa **aplicações** ou **ambas:**

1. No **Azure Ative Directory** no portal Azure, selecione **as inscrições da App**e, em seguida, selecione o registo da aplicação do cliente.
1. Em **Gestão**, selecione **permissões API**.
1. **Selecione Adicionar uma permissão**  >  **As Minhas APIs**.
1. Selecione a aplicação à qual adicionou a função de aplicação e, em seguida, selecione **permissões de aplicação**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as funções de aplicativo com os seguintes recursos:

- Amostra de código: [Adicionar autorização usando funções de aplicações & as funções reivindicam uma aplicação web core ASP.NET](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (GitHub)
- Vídeo: [Implementar autorização nas suas aplicações com plataforma de identidade microsoft ](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
- [Manifesto da aplicação do Azure Active Directory](./reference-app-manifest.md)
- [Fichas de acesso Azure AD](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
