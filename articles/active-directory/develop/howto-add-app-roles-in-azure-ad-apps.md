---
title: Como adicionar funções de aplicação no seu aplicativo registrado do Azure Active Directory e recebê-las no token
description: Saiba como adicionar funções de aplicação numa aplicação registada no Azure Active Directory, atribuir utilizadores e grupos a essas funções e recebê-las no `roles` de afirmações no token.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24e933399454942f4ee50440cffd791599679074
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299150"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Como: Adicionar funções de aplicação na sua aplicação e recebê-las no token

Controlo de acesso baseado em funções (RBAC) é um mecanismo popular para impor a autorização em aplicativos. Ao utilizar o RBAC, o administrador atribui permissões para funções e não para utilizadores individuais ou grupos. O administrador, em seguida, pode atribuir funções a usuários e grupos para controlar quem tem acesso para qual o conteúdo e funcionalidade diferentes.

Utilizando o RBAC com funções de aplicação e declarações de função, os desenvolvedores podem impor com segurança autorização nas suas aplicações com pouco esforço por parte dos mesmos.

Outra abordagem é usar grupos do Azure AD e declarações de grupo, conforme mostrado na [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Grupos do AD e funções de aplicação do Azure não são mutuamente exclusivas; eles podem ser usados em conjunto para fornecer controlo de acesso granulares ainda mais.

## <a name="declare-roles-for-an-application"></a>Declarar funções para uma aplicação

Estas funções de aplicação são definidas na [portal do Azure](https://portal.azure.com) no manifesto de registo da aplicação.  Quando um utilizador inicia sessão na aplicação, do Azure AD emite um `roles` afirmação para cada função que o utilizador recebeu individualmente para o utilizador e da respetiva associação a grupos.  Atribuição de utilizadores e grupos a funções pode ser feito através da IU do portal ou de forma programática com [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Declarar as funções de aplicação com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione a sua conta e, em seguida **trocar diretório**.
1. Uma vez a **diretório + subscrição** abrir do painel, selecione o inquilino do Active Directory em que deseja registar a aplicação, a partir do **Favoritos** ou **todos os diretórios**lista.
1. Selecione **todos os serviços** na barra de navegação esquerda e escolha **Azure Active Directory**.
1. Na **do Azure Active Directory** painel, selecione **registos das aplicações** para ver uma lista de todas as suas aplicações.

     Se não vir a aplicação que quer mostrar aqui, utilize os vários filtros na parte superior a **registos das aplicações** lista para restringir a lista ou desloque-se para baixo na lista para localizar seu aplicativo.

1. Selecione a aplicação que pretende definir funções de aplicação no.
1. No painel da sua aplicação, selecione **manifesto**.
1. Editar o manifesto da aplicação através da localização a `appRoles` definição e adicionar todas as funções de aplicação.

     > [!NOTE]
     > Cada definição de função de aplicação nesse manifesto tem de ter um GUID válido diferente para o `id` propriedade. 
     > 
     > O `value` propriedade de cada definição de função de aplicação deve corresponder exatamente as cadeias de caracteres que são utilizadas no código do aplicativo. O `value` propriedade não pode conter espaços. Se isso acontecer, receberá um erro ao guardar o manifesto.
     
1. Guarde o manifesto.

### <a name="examples"></a>Exemplos

A exemplo a seguir mostra a `appRoles` que pode atribuir a `users`.

> [!NOTE]
>O `id` tem de ser um GUID exclusivo.

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

Pode definir funções de aplicação de destino `users`, `applications`, ou ambos. Se estiver disponível para `applications`, as funções de aplicação são apresentados como permissões de aplicação no **permissões obrigatórias** painel. O exemplo seguinte mostra uma função de aplicação voltada para um `Application`.

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

O número de funções definidas afeta os limites que tenha o manifesto do aplicativo. Foi abordados em detalhes sobre o [limites de manifesto](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) página.

### <a name="assign-users-and-groups-to-roles"></a>Atribuir utilizadores e grupos a funções

Depois de adicionar funções de aplicações na sua aplicação, pode atribuir utilizadores e grupos a essas funções.

1. Na **do Azure Active Directory** painel, selecione **aplicações empresariais** do **Azure Active Directory** menu de navegação esquerdo.
1. Selecione **todos os aplicativos** para ver uma lista de todas as suas aplicações.

     Se não vir a aplicação que quer mostrar aqui, utilize os vários filtros na parte superior a **todos os aplicativos** lista para restringir a lista ou desloque-se para baixo na lista para localizar seu aplicativo.

1. Selecione a aplicação na qual pretende atribuir utilizadores ou grupo de segurança para as funções.
1. Selecione o **utilizadores e grupos** painel no menu de navegação esquerdo do aplicativo.
1. Na parte superior a **utilizadores e grupos** lista, selecione a **adicionar utilizador** botão para abrir o **adicionar atribuição** painel.
1. Selecione o **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

     Será apresentada uma lista de utilizadores e grupos de segurança, juntamente com uma caixa de texto para procurar e localizar um determinado usuário ou grupo. Este ecrã permite-lhe selecionar vários utilizadores e grupos de uma só vez.

1. Depois de concluída a selecionar os utilizadores e grupos, prima a **selecione** botão na parte inferior para avançar para a parte seguinte.
1. Escolha o **selecionar função** Seletor da **adicionar atribuição** painel. Todas as funções declaradas anteriormente no manifesto do aplicativo serão exibido.
1. Escolha uma função e prima a **selecione** botão.
1. Prima a **atribuir** botão na parte inferior para concluir as atribuições de utilizadores e grupos para a aplicação.
1. Confirme que os utilizadores e grupos que adicionou estão a aparecer no atualizada **utilizadores e grupos** lista.

## <a name="more-information"></a>Mais informações

- [Autorização na aplicação web através de funções de aplicação do Azure AD &amp; declarações de função (exemplo)](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapp-roleclaims/)
- [Utilizar grupos de segurança e funções de aplicação nas suas aplicações (vídeo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [O Azure Active Directory, agora com afirmações de grupo e funções de aplicação](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Manifesto da aplicação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Tokens de acesso do AAD](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)
