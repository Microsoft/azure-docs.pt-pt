---
title: Publique aplicativos de clientes nativos - Azure AD [ Microsoft Docs
description: Cobre como permitir que as aplicações de clientes nativos se comuniquem com o Azure Application Proxy Connector para fornecer acesso remoto seguro às suas aplicações no local.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 442e1515159afc1df79bb6f5f1f747ce0800fef7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647229"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Como permitir que as aplicações de clientes nativos interajam com aplicações de procuração

Pode utilizar o Proxy de Aplicação de Aplicação Do Diretório Ativo Azure (Azure AD) para publicar aplicações web, mas também pode ser usado para publicar aplicações de clientes nativos que estão configuradas com a Microsoft Authentication Library (MSAL). As aplicações de clientes nativos diferem das aplicações web porque estão instaladas num dispositivo, enquanto as aplicações web são acedidas através de um navegador.

Para apoiar as aplicações de clientes nativos, a Application Proxy aceita fichas emitidas pela Azure AD que são enviadas no cabeçalho. O serviço Proxy de Aplicação faz a autenticação para os utilizadores. Esta solução não utiliza fichas de aplicação para autenticação.

![Relação entre utilizadores finais, Azure AD, e aplicações publicadas](./media/application-proxy-configure-native-client-application/richclientflow.png)

Para publicar aplicações nativas, utilize a Biblioteca de Autenticação da Microsoft, que cuida da autenticação e suporta muitos ambientes de clientes. Application Proxy [enquadra-se na aplicação Desktop que chama uma API web em nome de um](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user) cenário de utilizador assinado.

Este artigo percorre os quatro passos para publicar uma aplicação nativa com a Application Proxy e a Biblioteca de Autenticação AD Azure.

## <a name="step-1-publish-your-proxy-application"></a>Passo 1: Publique a sua aplicação proxy

Publique a sua aplicação proxy como qualquer outra aplicação e atribua aos utilizadores o acesso à sua aplicação. Para mais informações, consulte [Publicar aplicações com Procuração de Aplicações](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Passo 2: Registe a sua aplicação nativa

Agora precisa de registar a sua candidatura em Azure AD, da seguinte forma:

1. Inscreva-se no [portal de Diretório Ativo Azure.](https://aad.portal.azure.com/) Aparece o **Dashboard** para o centro de **administração do Diretório Ativo Azure.**
1. Na barra lateral, selecione **Azure Ative Directory**. Aparece a página de visão geral do **Diretório Ativo Azure.**
1. Na barra lateral azure AD, selecione registos de **Aplicações**. A lista de todos os registos de aplicações aparece.
1. Selecione **Novo registo**. Aparece uma página de inscrição no **Registo.**

   ![Criar uma nova inscrição de aplicações no portal Azure](./media/application-proxy-configure-native-client-application/create.png)

1. Na rubrica **Nome,** especifique um nome de visualização virado para o utilizador para a sua aplicação.
1. Na rubrica de tipos de **conta suportada,** selecione um nível de acesso utilizando estas orientações:

   - Para direcionar apenas contas internas para a sua organização, selecione **Contas apenas neste diretório organizacional**.
   - Para direcionar apenas clientes empresariais ou educativos, selecione **Contas em qualquer diretório organizacional.**
   - Para direcionar o conjunto mais amplo de identidades da Microsoft, selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
1. Em **Redirect URI,** selecione **cliente público (mobile & desktop)** e, em seguida, digite o URI redirecionamento para a sua `https://login.microsoftonline.com/common/oauth2/nativeclient` aplicação.
1. Selecione e leia as Políticas da **Plataforma Microsoft,** e, em seguida, selecione **Register**. É criada e apresentada uma página geral para o novo registo de candidaturas.

Para obter informações mais detalhadas sobre a criação de um novo registo de candidaturas, consulte a Integração de [aplicações com o Diretório Ativo do Azure.](../develop/quickstart-register-app.md)

## <a name="step-3-grant-access-to-your-proxy-application"></a>Passo 3: Conceder acesso ao seu pedido de procuração

Agora que registou a sua aplicação nativa, pode dar-lhe acesso a outras aplicações no seu diretório, neste caso para aceder à aplicação de procuração. Para permitir que a aplicação nativa seja exposta à aplicação por procuração:

1. Na barra lateral da nova página de registo de candidaturas, selecione **permissões API**. Aparece a página de **permissões** da API para o novo registo de candidaturas.
1. Selecione **Adicionar uma permissão**. Aparece a página de **permissões da API request.**
1. Sob a definição **Select a API,** selecione **APIs que a minha organização utiliza**. Aparece uma lista, contendo as aplicações no seu diretório que expõem APIs.
1. Digite na caixa de pesquisa ou percorra para encontrar a aplicação proxy que publicou no Passo 1: Publique a [sua aplicação proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-native-client-application#step-1-publish-your-proxy-application), e, em seguida, selecione a aplicação proxy.
1. No **tipo de permissões que o seu pedido requer?** Se a sua aplicação nativa necessitar de aceder à aplicação proxy API como utilizador inscrito, escolha **permissões delegadas**.
1. Na rubrica **'Selecionar permissões',** selecione a permissão desejada e selecione **Adicionar permissões**. A página de **permissões da API** para a sua aplicação nativa mostra agora o pedido de procuração e a permissão API que adicionou.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>Passo 4: Adicione a Biblioteca de Autenticação da Microsoft ao seu código (.NET C# sample)

Editar o código de aplicação nativa no contexto de autenticação da Microsoft Authentication Library (MSAL) para incluir o seguinte texto: 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

As informações necessárias no código da amostra podem ser encontradas no portal Azure AD, da seguinte forma:

| Informações necessárias | Como encontrá-lo no portal Azure AD |
| --- | --- |
| \<> de identificação do inquilino | Diretório Ativo **Azure**  >  **Propriedades**  >  **ID do diretório** |
| \<Id da app nativa> | **Inscrição de candidatura**  >  *sua aplicação*  >  nativa **Visão geral**  >  **ID de aplicação** |
| \<Âmbito> | **Inscrição de candidatura**  >  *sua aplicação*  >  nativa **Permissões API** > Clique na API de Permissão (user_impersonation) > Painel Com a legenda **user_impersonation** aparece no lado direito. > O âmbito é o URL na caixa de edição.
| \<Url de aplicação proxy> | o Url Externo e o caminho para a API

Depois de editar o código MSAL com estes parâmetros, os seus utilizadores podem autenticar aplicações de clientes nativos mesmo quando estão fora da rede corporativa.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o fluxo de aplicações nativas, consulte [aplicações nativas no Diretório Ativo Azure.](../azuread-dev/native-app.md)

Saiba mais sobre a criação de um [único sign-on para aplicações no Diretório Ativo Azure](what-is-single-sign-on.md#choosing-a-single-sign-on-method).