---
title: Adicionar funções de aplicativo em seu aplicativo registrado Azure Active Directory e recebê-las no token
titleSuffix: Microsoft identity platform
description: Saiba como adicionar funções de aplicativo em um aplicativo registrado no Azure Active Directory, atribuir usuários e grupos a essas funções e recebê-las na declaração de `roles` no token.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fca807913d2753828d55c095c1a3b380340fd95
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843160"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token

O RBAC (controle de acesso baseado em função) é um mecanismo popular para impor a autorização em aplicativos. Ao usar o RBAC, um administrador concede permissões a funções, e não a usuários individuais ou grupos. O administrador pode atribuir funções a diferentes usuários e grupos para controlar quem tem acesso a qual conteúdo e funcionalidade.

Usando o RBAC com funções de aplicativo e declarações de função, os desenvolvedores podem impor com segurança a autorização em seus aplicativos com pouco esforço em sua parte.

Outra abordagem é usar grupos e declarações de grupo do Azure AD, conforme mostrado em [webapp-GroupClaims-dotnet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Os grupos do Azure AD e as funções de aplicativo não são mutuamente exclusivos; Eles podem ser usados em conjunto para fornecer um controle de acesso ainda mais refinado.

## <a name="declare-roles-for-an-application"></a>Declarar funções para um aplicativo

Essas funções de aplicativo são definidas no [portal do Azure](https://portal.azure.com) no manifesto de registro do aplicativo.  Quando um usuário entra no aplicativo, o Azure AD emite uma declaração de `roles` para cada função que o usuário recebeu individualmente para o usuário e de sua associação de grupo.  A atribuição de usuários e grupos a funções pode ser feita por meio da interface do usuário do portal ou por meio de programação usando [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Declarar funções de aplicativo usando portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione sua conta e, em seguida, **Alterne o diretório**.
1. Depois que o painel **diretório + assinatura** for aberto, escolha o Active Directory locatário no qual você deseja registrar seu aplicativo, na lista **favoritos** ou **todos os diretórios** .
1. Selecione **todos os serviços** no painel de navegação à esquerda e escolha **Azure Active Directory**.
1. No painel de **Azure Active Directory** , selecione **registros de aplicativo** para exibir uma lista de todos os seus aplicativos.
1. Selecione o aplicativo no qual você deseja definir funções de aplicativo. Em seguida, selecione **manifesto**.
1. Edite o manifesto do aplicativo localizando a configuração de `appRoles` e adicionando todas as suas funções de aplicativo.

     > [!NOTE]
     > Cada definição de função de aplicativo neste manifesto deve ter um GUID válido diferente para a propriedade `id`. 
     > 
     > A propriedade `value` de cada definição de função de aplicativo deve corresponder exatamente às cadeias de caracteres usadas no código no aplicativo. A propriedade `value` não pode conter espaços. Se tiver, você receberá um erro ao salvar o manifesto.
     
1. Salve o manifesto.

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra a `appRoles` que você pode atribuir a `users`.

> [!NOTE]
>O `id` deve ser um GUID exclusivo.

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
>O `displayName` não pode conter espaços.

Você pode definir funções de aplicativo para `users`de destino, `applications`ou ambos. Quando disponível para `applications`, as funções de aplicativo aparecem como permissões de aplicativo na folha **permissões necessárias** . O exemplo a seguir mostra uma função de aplicativo direcionada para um `Application`.

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

O número de funções definidas afeta os limites que o manifesto do aplicativo tem. Eles foram discutidos em detalhes na página [limites do manifesto](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) .

### <a name="assign-users-and-groups-to-roles"></a>Atribuir usuários e grupos a funções

Depois de adicionar funções de aplicativo em seu aplicativo, você pode atribuir usuários e grupos a essas funções.

1. No painel de **Azure Active Directory** , selecione **aplicativos empresariais** no **Azure Active Directory** menu de navegação à esquerda.
1. Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

     Se você não vir o aplicativo que deseja exibir aqui, use os vários filtros na parte superior da lista **todos os aplicativos** para restringir a lista ou role para baixo na lista para localizar seu aplicativo.

1. Selecione o aplicativo ao qual você deseja atribuir usuários ou grupo de segurança a funções.
1. Selecione o painel **usuários e grupos** no menu de navegação esquerdo do aplicativo.
1. Na parte superior da lista **usuários e grupos** , selecione o botão **Adicionar usuário** para abrir o painel **Adicionar atribuição** .
1. Selecione o seletor **usuários e grupos** no painel **Adicionar atribuição** .

     Uma lista de usuários e grupos de segurança será mostrada junto com uma caixa de texto para pesquisar e localizar um determinado usuário ou grupo. Esta tela permite que você selecione vários usuários e grupos em um só lugar.

1. Quando terminar de selecionar os usuários e grupos, pressione o botão **selecionar** na parte inferior para ir para a próxima parte.
1. Escolha o seletor **selecionar função** no painel **Adicionar atribuição** . Todas as funções declaradas anteriormente no manifesto do aplicativo aparecerão.
1. Escolha uma função e pressione o botão **selecionar** .
1. Pressione o botão **atribuir** na parte inferior para concluir as atribuições de usuários e grupos ao aplicativo.
1. Confirme se os usuários e grupos que você adicionou estão aparecendo na lista **usuários e grupos** atualizados.

## <a name="more-information"></a>Mais informações

- [Autorização em um aplicativo Web usando funções de aplicativo do Azure AD &amp; declarações de função (exemplo)](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims)
- [Usando grupos de segurança e funções de aplicativo em seus aplicativos (vídeo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, agora com declarações de grupo e funções de aplicativo](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Manifesto do aplicativo Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Tokens de acesso do AAD](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)
