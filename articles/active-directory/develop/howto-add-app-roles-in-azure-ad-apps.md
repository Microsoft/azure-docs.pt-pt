---
title: Adicione as funções de aplicativo e obtenha-as a partir de um token ! Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como adicionar funções de aplicação numa aplicação registada no Azure Ative Directory, atribua utilizadores e grupos a estas funções e receba-as `roles` na reivindicação no token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: be5cb1c1e6ff428b3c4d4305c915e07d3880839c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258392"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Como: Adicionar funções de aplicação na sua aplicação e recebê-las no token

O controlo de acesso baseado em funções (RBAC) é um mecanismo popular para impor a autorização em aplicações. Ao utilizar o RBAC, um administrador concede permissões a funções e não a utilizadores ou grupos individuais. O administrador pode então atribuir funções a diferentes utilizadores e grupos para controlar quem tem acesso a que conteúdo e funcionalidade.

Utilizando o RBAC com Funções de Aplicação e Reivindicações de Funções, os desenvolvedores podem impor de forma segura a autorização nas suas apps com pouco esforço da sua parte.

Outra abordagem é utilizar grupos AD Ad E Reivindicações de Grupo, como mostrado no [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Os grupos AD Azure e as Funções de Aplicação não são, de forma alguma, mutuamente exclusivos; podem ser usados em conjunto para fornecer um controlo de acesso ainda mais fino.

## <a name="declare-roles-for-an-application"></a>Declarar funções para uma aplicação

Estas funções de candidatura são definidas no [portal Azure](https://portal.azure.com) no manifesto de inscrição da candidatura.  Quando um utilizador assina a aplicação, a Azure AD emite uma `roles` reclamação por cada papel que o utilizador tenha sido concedido individualmente ao utilizador e da sua filiação em grupo.  A atribuição de utilizadores e grupos a funções pode ser feita através da UI do portal, ou utilizando programáticamente [o Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Declare funções de aplicativo usando o portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de inscrição + Diretório** na barra de ferramentas do portal.
1. Na lista **de Favoritos** ou **Todos os Diretórios,** escolha o inquilino ative directory onde deseja registar a sua candidatura.
1. No portal Azure, procure e selecione **O Diretório Ativo Azure**.
1. No painel  **Azure Ative Directory,** selecione **registos de Aplicações** para ver uma lista de todas as suas aplicações.
1. Selecione a aplicação em que pretende definir funções de aplicação. Em seguida, selecione **Manifesto**.
1. Edite o manifesto da aplicação localizando a `appRoles` definição e adicionando todas as suas Funções de Aplicação.

     > [!NOTE]
     > Cada definição de função de aplicação neste manifesto deve ter um GUID diferente e válido no contexto do manifesto para o `id` imóvel.
     >
     > A `value` propriedade de cada definição de função da aplicação deve corresponder exatamente às cordas que são usadas no código na aplicação. A `value` propriedade não pode conter espaços. Se isso acontecer, receberá um erro quando guardar o manifesto.

1. Salve o manifesto.

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra o `appRoles` que pode atribuir `users` .

> [!NOTE]
>Deve `id` ser um GUID único.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>O `displayName` pode conter espaços.

Pode definir funções de aplicativo para alvo `users` , `applications` ou ambos. Quando disponível para `applications` , as funções da aplicação aparecem como permissões de aplicação na secção **Gerir** > **permissões de API > Adicionar uma permissão > As minhas APIs > Escolha uma API > Permissões de Aplicação**. O exemplo a seguir mostra uma função de aplicação direcionada para um `Application` .

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

O número de funções definidas afeta os limites que o manifesto de aplicação tem. Foram discutidos em pormenor na página de [limites manifestos.](./reference-app-manifest.md#manifest-limits)

### <a name="assign-users-and-groups-to-roles"></a>Atribuir utilizadores e grupos a funções

Uma vez adicionadas funções de aplicação na sua aplicação, pode atribuir utilizadores e grupos a estas funções.

1. No painel **de diretório ativo Azure,** selecione **aplicações Enterprise** do menu de navegação à esquerda do **Azure Ative.**
1. Selecione **Todas as aplicações** para ver uma lista de todas as suas aplicações.

     Se não vir a aplicação que pretende aparecer aqui em cima, utilize os vários filtros no topo da lista de **aplicações** para restringir a lista ou desloque a lista para localizar a sua aplicação.

1. Selecione a aplicação na qual pretende atribuir utilizadores ou grupo de segurança a funções.
1. Selecione o painel **de Utilizadores e grupos** no menu de navegação à esquerda da aplicação.
1. No topo da lista de **Utilizadores e grupos,** selecione o botão **de utilizador Adicionar** para abrir o painel de atribuição de **adicionar.**
1. Selecione o seletor **de Utilizadores e grupos** a partir do painel **de atribuição de adicionar.**

     Será apresentada uma lista de utilizadores e grupos de segurança juntamente com uma caixa de texto para pesquisar e localizar um determinado utilizador ou grupo. Este ecrã permite-lhe selecionar vários utilizadores e grupos de uma só vez.

1. Uma vez terminado a seleção dos utilizadores e grupos, prima o botão **Select** on bottom para passar para a parte seguinte.
1. Escolha o seletor **de funções Select** a partir do painel de **atribuição Adicionar.** Todos os papéis declarados anteriormente no manifesto da aplicação aparecerão.
1. Escolha uma função e prima o botão **Select.**
1. Pressione o botão **Atribuir** na parte inferior para terminar as atribuições de utilizadores e grupos à aplicação.
1. Confirme que os utilizadores e grupos que adicionou estão a aparecer na lista atualizada **de Utilizadores e grupos.**

### <a name="receive-roles-in-tokens"></a>Receber papéis em fichas

Quando os utilizadores atribuídos às várias funções da aplicação iniciarem o sessão na aplicação, os seus tokens terão as suas funções atribuídas na `roles` reclamação.

## <a name="next-steps"></a>Passos seguintes

- [Adicione autorização usando funções de aplicações & as funções reivindicam uma aplicação web core ASP.NET](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Implementar autorização nas suas aplicações com a plataforma de identidade microsoft (Vídeo)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Diretório Ativo Azure, agora com Reivindicações de Grupo e Funções de Aplicação](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifesto da aplicação do Azure Active Directory](./reference-app-manifest.md)
- [Fichas de acesso Azure AD](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
