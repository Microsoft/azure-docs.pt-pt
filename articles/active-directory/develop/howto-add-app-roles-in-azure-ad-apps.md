---
title: Adicione as funções da aplicação e obtenha-as de um símbolo / Azure
titleSuffix: Microsoft identity platform
description: Saiba como adicionar funções de aplicação numa aplicação registada no Azure Ative Directory, atribuir utilizadores e grupos a estas funções e recebê-las na `roles` reclamação no token.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3a911db36fd03ebcb5e0fc53d4d7f36d68648249
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399082"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Como: Adicionar funções de aplicação na sua aplicação e recebê-las no símbolo

O controlo de acesso baseado em funções (RBAC) é um mecanismo popular para impor a autorização nos pedidos. Ao utilizar o RBAC, um administrador concede permissões a funções e não a utilizadores ou grupos individuais. O administrador pode então atribuir funções a diferentes utilizadores e grupos para controlar quem tem acesso a que conteúdo e funcionalidade.

Utilizando o RBAC com Funções de Aplicação e Reivindicações de Funções, os desenvolvedores podem impor de forma segura a autorização nas suas apps com pouco esforço da sua parte.

Outra abordagem é utilizar os Grupos AD Azure e as Reivindicações de Grupo, como mostra o [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Os grupos ad-ad-azur e as funções de aplicação não são, de modo algum, mutuamente exclusivos; podem ser utilizados em conjunto para fornecer um controlo de acesso ainda mais fino.

## <a name="declare-roles-for-an-application"></a>Declarar funções para uma candidatura

Estas funções de candidatura são definidas no [portal Azure](https://portal.azure.com) no manifesto de registo da aplicação.  Quando um utilizador assina na aplicação, a Azure AD emite uma `roles` pedido de cada função de que o utilizador foi concedido individualmente ao utilizador e à sua adesão ao grupo.  A atribuição de utilizadores e grupos para funções pode ser feita através do UI do portal, ou utilizando programáticamente o [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Declarar funções de aplicação usando o portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal.
1. Na lista de Favoritos ou **Todos os Diretórios,** escolha o inquilino do Diretório Ativo onde deseja registar a sua candidatura.
1. No portal Azure, procure e selecione **Azure Ative Directory**.
1. No painel **de Diretório Ativo Azure,** selecione registos de **Aplicações** para visualizar uma lista de todas as suas aplicações.
1. Selecione a aplicação em que pretende definir as funções da aplicação. Em seguida, selecione **Manifesto**.
1. Editar o manifesto da aplicação localizando a definição de `appRoles` e adicionando todas as suas Funções de Aplicação.

     > [!NOTE]
     > Cada definição de função da aplicação neste manifesto deve ter um GUID válido diferente no contexto do manifesto para a propriedade `id`.
     >
     > A propriedade `value` de cada definição de função de aplicação deve corresponder exatamente às cordas que são usadas no código da aplicação. A propriedade `value` não pode conter espaços. Se isso acontecer, receberá um erro quando guardar o manifesto.

1. Salve o manifesto.

### <a name="examples"></a>Exemplos

O exemplo que se segue mostra o `appRoles` que pode atribuir a `users`.

> [!NOTE]
>O `id` deve ser um GUID único.

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
>A `displayName` não pode conter espaços.

Pode definir funções de aplicativo saem para `users`, `applications`, ou ambos. Quando disponível para `applications`, as funções da aplicação aparecem como permissões de aplicação na lâmina **de Permissões Necessárias.** O exemplo seguinte mostra um papel de aplicação direcionado para um `Application`.

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

O número de funções definidas afeta os limites que o manifesto de candidatura tem. Foram discutidos em pormenor na página dos [limites manifestos.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits)

### <a name="assign-users-and-groups-to-roles"></a>Atribuir utilizadores e grupos a funções

Depois de adicionar funções de aplicação na sua aplicação, pode atribuir utilizadores e grupos a estas funções.

1. No painel **azure Ative Directory,** selecione **aplicações Enterprise** do menu de navegação à esquerda **do Azure Ative Diretório.**
1. Selecione **Todas as aplicações** para visualizar uma lista de todas as suas aplicações.

     Se não vir a aplicação que pretende aparecer aqui, utilize os vários filtros no topo da lista **de aplicações para** restringir a lista ou desloque a lista para localizar a sua aplicação.

1. Selecione a aplicação na qual pretende atribuir aos utilizadores ou grupo de segurança as funções.
1. Selecione o painel **de utilizadores e grupos** no menu de navegação à esquerda da aplicação.
1. Na parte superior da lista de **Utilizadores e grupos,** selecione o botão **adicionar** para abrir o painel **adicionar assignment.**
1. Selecione o seletor de **utilizadores e grupos** do painel **adicionar** assignment.

     Uma lista de utilizadores e grupos de segurança será mostrada juntamente com uma caixa de texto para pesquisar e localizar um determinado utilizador ou grupo. Este ecrã permite selecionar vários utilizadores e grupos de uma só vez.

1. Uma vez feito a seleção dos utilizadores e grupos, prima o botão **Select** na parte inferior para passar para a próxima parte.
1. Escolha o seletor de **funções Select** a partir do painel de **atribuição Adicionar.** Todos os papéis declarados anteriormente no manifesto da aplicação aparecerão.
1. Escolha uma função e prima o botão **Select.**
1. Pressione o botão **De atribuição** na parte inferior para terminar as atribuições de utilizadores e grupos para a aplicação.
1. Confirme que os utilizadores e grupos que adicionou estão a aparecer na lista de **Utilizadores e grupos atualizados.**

## <a name="more-information"></a>Mais informações

- [Adicionar autorização usando funções de aplicativos e pedidos de funções para uma aplicação web core ASP.NET](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Utilização de Grupos de Segurança e Funções de Aplicação nas suas apps (Vídeo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Diretório Ativo Azure, agora com reivindicações de grupo e funções de candidatura](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifesto de aplicativo sony Azure Ative Diretório](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Fichas de acesso AAD](access-tokens.md)
- [`id_tokens` aD](id-tokens.md)
