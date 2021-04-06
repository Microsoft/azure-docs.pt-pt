---
title: Adicione os papéis de aplicativo e obtenha-os de um símbolo | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como adicionar funções de aplicação a uma aplicação registada no Azure Ative Directory, atribuir utilizadores e grupos a estas funções e recebê-las na reivindicação de 'roles' no token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: fce963bd9ffdc6f768d7b3de4a9e4870add06136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104251"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>Como: Adicionar funções de aplicação à sua aplicação e recebê-las no token

O controlo de acesso baseado em funções (RBAC) é um mecanismo popular para impor a autorização em aplicações. Ao utilizar o RBAC, um administrador concede permissões a funções e não a utilizadores ou grupos individuais. O administrador pode então atribuir funções a diferentes utilizadores e grupos para controlar quem tem acesso a que conteúdo e funcionalidade.

Utilizando o RBAC com Funções de Aplicação e Reivindicações de Funções, os desenvolvedores podem impor de forma segura a autorização nas suas apps com menos esforço.

Outra abordagem é utilizar os grupos AD E as Reivindicações de Grupo Azure, como mostrado na amostra de código [activa-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample) no GitHub. Os grupos AD Azure e as Funções de Aplicação não são mutuamente exclusivos; podem ser usados em conjunto para fornecer um controlo de acesso ainda mais fino.

## <a name="declare-roles-for-an-application"></a>Declarar funções para uma aplicação

Define as funções de aplicação utilizando o [portal Azure.](https://portal.azure.com) As funções de aplicação são geralmente definidas num registo de aplicação representando um serviço, app ou API. Quando um utilizador assina a aplicação, a Azure AD emite uma `roles` reclamação por cada papel que o utilizador ou titular do serviço foi concedido individualmente ao utilizador e da sua filiação em grupo. Isto pode ser usado para implementar uma autorização baseada em sinistros. As funções da aplicação podem ser atribuídas [a um utilizador ou a um grupo de utilizadores.](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app) As funções da app também podem ser atribuídas ao principal do serviço para outra aplicação, ou [ao principal do serviço para uma identidade gerida.](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md)

> [!IMPORTANT]
> Atualmente, se adicionar um principal de serviço a um grupo e, em seguida, atribuir um papel de app a esse grupo, a Azure AD não adiciona a `roles` alegação aos tokens que problema.

Existem duas formas de declarar os papéis da aplicação utilizando o portal Azure:

* [Funções de aplicativo UI](#app-roles-ui--preview) | previsualizar
* [Editor manifesto de aplicativo](#app-manifest-editor)

O número de funções que adiciona conta para os limites manifestos de aplicação imposto pelo Azure Ative Directory. Para obter informações sobre estes limites, consulte a secção [Desproteturas](./reference-app-manifest.md#manifest-limits) da [aplicação Azure Ative Directory.](reference-app-manifest.md)

### <a name="app-roles-ui--preview"></a>Funções de aplicativo UI | previsualizar

> [!IMPORTANT]
> A funcionalidade UI do portal de funções de aplicativos [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Para criar uma função de aplicação utilizando a interface de utilizador do portal Azure:

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Selecione o filtro **de subscrição Directory +** no menu superior e, em seguida, escolha o inquilino do Azure Ative Directory que contém o registo da aplicação ao qual pretende adicionar uma função de app.
1. Procure e selecione **Azure Active Directory**.
1. Em **Manage**, selecione **registos de Aplicações** e, em seguida, selecione a aplicação em que pretende definir funções de aplicação.
1. Selecione **funções de aplicação | Pré-visualizar** e, em seguida, selecionar **Criar o papel da aplicação**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Um painel de funções de aplicação de registo de aplicações no portal Azure":::
1. No painel de funções da **aplicação Create,** introduza as definições para o papel. A tabela que segue a imagem descreve cada definição e os seus parâmetros.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="As funções de aplicação de um registo de aplicações criam um painel de contexto no portal Azure":::

    | Campo | Descrição | Exemplo |
    |-------|-------------|---------|
    | **Nome a apresentar** | Mostrar o nome para o papel da aplicação que aparece nas experiências de consentimento de administração e atribuição de aplicações. Este valor pode conter espaços. | `Survey Writer` |
    | **Tipos de membros permitidos** | Especifica se esta função da app pode ser atribuída a utilizadores, aplicações ou ambos.<br/><br/>Quando disponível para `applications` , as funções da aplicação aparecem como permissões de aplicação na secção **de Gestão** de uma aplicação > **permissões de API > Adicionar uma permissão > As minhas APIs > Escolha uma API > Permissões de Aplicação.** | `Users/Groups` |
    | **Valor** | Especifica o valor das funções alegando que a aplicação deve esperar no token. O valor deve corresponder exatamente à cadeia referenciada no código da aplicação. O valor não pode conter espaços. | `Survey.Create` |
    | **Descrição** | Uma descrição mais detalhada da função da aplicação exibida durante a atribuição de aplicações de administração e experiências de consentimento. | `Writers can create surveys.` |
    | **Deseja ativar esta função de aplicação?** | Especifica se a função da aplicação está ativada. Para eliminar uma função de aplicação, desmarcar esta caixa de verificação e aplicar a alteração antes de tentar a operação de eliminação. | *Verificado* |

1. Selecione **Aplicar** para guardar as alterações.

### <a name="app-manifest-editor"></a>Editor manifesto de aplicativo

Para adicionar papéis editando o manifesto diretamente:

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Selecione o filtro **de subscrição Directory +** no menu superior e, em seguida, escolha o inquilino do Azure Ative Directory que contém o registo da aplicação ao qual pretende adicionar uma função de app.
1. Procure e selecione **Azure Active Directory**.
1. Em **Manage**, selecione **registos de Aplicações** e, em seguida, selecione a aplicação em que pretende definir funções de aplicação.
1. Novamente em **Manage**, selecione **Manifesto**.
1. Edite o manifesto da aplicação localizando a `appRoles` definição e adicionando as suas funções de aplicação. Pode definir funções de aplicativo que `users` `applications` visam, ou ambos. Os seguintes snippets JSON mostram exemplos de ambos.
1. Salve o manifesto.

Cada definição de função de aplicação no manifesto deve ter um GUID único para o seu `id` valor.

A `value` propriedade de cada definição de função da aplicação deve corresponder exatamente às cordas que são usadas no código na aplicação. A `value` propriedade não pode conter espaços. Se isso acontecer, receberá um erro quando guardar o manifesto.

#### <a name="example-user-app-role"></a>Exemplo: Função de aplicativo de utilizador

Este exemplo define uma função de aplicação nomeada `Writer` que pode atribuir `User` a:

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>Exemplo: Função de aplicação de aplicação de aplicação de aplicações

Quando disponível para `applications` , as funções da aplicação aparecem como permissões de aplicação na secção **de Gestão** de uma aplicação > **permissões de API > Adicionar uma permissão > As minhas APIs > Escolha uma API > Permissões de Aplicação.**

Este exemplo mostra uma função de aplicação direcionada `Application` a:

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>Atribuir utilizadores e grupos a funções

Uma vez adicionadas funções de aplicação na sua aplicação, pode atribuir utilizadores e grupos às funções. A atribuição de utilizadores e grupos a funções pode ser feita através da UI do portal, ou utilizando programáticamente [o Microsoft Graph](/graph/api/user-post-approleassignments). Quando os utilizadores atribuídos às várias funções da aplicação iniciarem o sessão na aplicação, os seus tokens terão as suas funções atribuídas na `roles` reclamação.

Para atribuir utilizadores e grupos a funções utilizando o portal Azure:

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. No **Azure Ative Directory,** selecione **aplicações Enterprise** no menu de navegação à esquerda.
1. Selecione **Todas as aplicações** para ver uma lista de todas as suas aplicações. Se a sua aplicação não aparecer na lista, utilize os filtros no topo da lista de **aplicações** para restringir a lista, ou desloque a lista para localizar a sua aplicação.
1. Selecione a aplicação na qual pretende atribuir utilizadores ou grupo de segurança a funções.
1. Em **Gerir**, selecione **Utilizadores e grupos**.
1. **Selecione Adicionar** ao utilizador para abrir o painel de atribuição **de adicionar.**
1. Selecione o seletor **de Utilizadores e grupos** a partir do painel **de atribuição de adicionar.** É apresentada uma lista de utilizadores e grupos de segurança. Pode pesquisar por um determinado utilizador ou grupo, bem como selecionar vários utilizadores e grupos que aparecem na lista.
1. Uma vez selecionados utilizadores e grupos, selecione o botão **Select** para proceder.
1. **Selecione Selecionar uma função** no painel **de atribuição Adicionar.** Todos os papéis que definiu para a aplicação são apresentados.
1. Escolha uma função e selecione o botão **Select.**
1. Selecione o botão **Atribuir** para terminar a atribuição de utilizadores e grupos para a aplicação.

Confirme que os utilizadores e grupos adicionados aparecem na lista **de Utilizadores e grupos.**

## <a name="assign-app-roles-to-applications"></a>Atribuir funções de aplicações a aplicações

Uma vez adicionados papéis de aplicação na sua aplicação, pode atribuir um papel de aplicação a uma aplicação de clientes utilizando o portal Azure ou programáticamente utilizando o [Microsoft Graph](/graph/api/user-post-approleassignments).

Quando atribui funções de aplicação a uma aplicação, cria *permissões de aplicação.* As permissões de aplicação são normalmente utilizadas por apps daemon ou serviços de back-end que precisam de autenticar e fazer chamadas autorizadas de API como si mesmos, sem a interação de um utilizador.

Para atribuir funções de aplicação a uma aplicação utilizando o portal Azure:

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. No **Azure Ative Directory,** selecione **registos de Aplicações** no menu de navegação à esquerda.
1. Selecione **Todas as aplicações** para ver uma lista de todas as suas aplicações. Se a sua aplicação não aparecer na lista, utilize os filtros no topo da lista de **aplicações** para restringir a lista, ou desloque a lista para localizar a sua aplicação.
1. Selecione a aplicação à qual pretende atribuir uma função de aplicação.
1. Selecione **permissões API**  >  **Adicione uma permissão**.
1. Selecione o **separador My APIs** e, em seguida, selecione a aplicação para a qual definiu as funções da aplicação.
1. Selecione **permissões de aplicação**.
1. Selecione as funções que pretende atribuir.
1. Selecione o botão **adicionar permissões** completa adição da(s) função.

As funções recém-adicionadas devem aparecer no painel de **permissões API** do seu registo de aplicações.

#### <a name="grant-admin-consent"></a>Conceder consentimento administrativo

Como se trata *de permissões de aplicação*, não permissões delegadas, um administrador deve conceder o consentimento para usar as funções da aplicação atribuídas à aplicação.

1. No painel de **permissões** de API do registo da aplicação, selecione **o consentimento de administração grant para \<tenant name\>**.
1. Selecione **Sim** quando solicitado para conceder o consentimento para as permissões solicitadas.

A coluna **status** deve refletir que o consentimento foi **concedido para \<tenant name\>**.

## <a name="use-app-roles-in-your-web-api"></a>Use funções de aplicativo na sua API web

Uma vez definidas as funções da aplicação e as atribuiu a um utilizador, grupo ou aplicação, o seu próximo passo é adicionar código à sua API web que verifica essas funções quando a API é chamada. Ou seja, quando uma aplicação de clientes solicita uma operação API que decidiu necessitar de autorização, o código da API deve verificar se os âmbitos estão no token de acesso apresentado na chamada da app do cliente.

Para aprender a adicionar autorização à sua API web, consulte [a API web protegida: Verifique os âmbitos e as funções de aplicação](scenario-protected-web-api-verification-scope-app-roles.md).

## <a name="app-roles-vs-groups"></a>Funções de aplicativo vs grupos

Embora possa utilizar funções de aplicações ou grupos para autorização, as principais diferenças entre elas podem influenciar as quais decide usar para o seu cenário.

| Funções de aplicativo                                                                          | Grupos                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| São específicos de uma aplicação e são definidos no registo da aplicação. Eles movem-se com a aplicação. | Não são específicos de uma aplicação, mas de um inquilino da AD Azure. |
| As funções da aplicação são removidas quando o registo da aplicação é removido.                      | Os grupos permanecem intactos mesmo que a aplicação seja removida.            |
| Fornecido no `roles` pedido.                                                     | Fornecido em `groups` reclamação.                                 |

Os desenvolvedores podem usar as funções de aplicação para controlar se um utilizador pode iniciar sôm numa aplicação ou uma aplicação pode obter um token de acesso para uma API web. Para alargar este controlo de segurança a grupos, os desenvolvedores e administradores também podem atribuir grupos de segurança a funções de aplicações.

As funções de aplicação são preferidas pelos desenvolvedores quando querem descrever e controlar os parâmetros de autorização na sua própria app. Por exemplo, uma aplicação que usa grupos para autorização irá quebrar o próximo inquilino, uma vez que tanto o ID do grupo como o nome poderiam ser diferentes. Uma aplicação que usa funções de aplicativo permanece segura. Na verdade, atribuir grupos a funções de apps é popular entre as aplicações do SaaS pelas mesmas razões.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre funções de aplicativos com os seguintes recursos.

* Exemplos de código no GitHub
  * [Adicionar autorização usando grupos e reivindicações de grupo a uma aplicação web core ASP.NET](https://aka.ms/groupssample)
  * [Aplicação de página única angular (SPA) chamando a API web .NET Core e usando funções de aplicativo e grupos de segurança](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* Documentação de referência
  * [Manifesto de aplicação AD AZure](./reference-app-manifest.md)
  * [Fichas de acesso AZure AD](access-tokens.md)
  * [Fichas de ID AD AZure](id-tokens.md)
  * [Fornecer reclamações opcionais à sua app](active-directory-optional-claims.md)
* Vídeo: [Implementar autorização nas suas aplicações com plataforma de identidade microsoft](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
