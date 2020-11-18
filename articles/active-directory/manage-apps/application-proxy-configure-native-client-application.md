---
title: Publicar aplicativos de clientes nativos - Azure AD / Microsoft Docs
description: Abrange como permitir que as aplicações de clientes nativos comuniquem com o Azure AD Application Proxy Connector para fornecer acesso remoto seguro às suas aplicações no local.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09e813e0dff09885f104e43099b10d6f6f8a7f9b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657436"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Como permitir que as aplicações de clientes nativos interajam com aplicações de procuração

Pode utilizar o Azure Ative Directory (Azure AD) Application Proxy para publicar aplicações web, mas também pode ser usado para publicar aplicações de clientes nativos que estão configuradas com a Microsoft Authentication Library (MSAL). As aplicações de clientes nativos diferem das aplicações web porque estão instaladas num dispositivo, enquanto as aplicações web são acedidas através de um navegador.

Para suportar aplicações de clientes nativos, a Application Proxy aceita fichas emitidas pela AD Azure que são enviadas no cabeçalho. O serviço Application Proxy faz a autenticação para os utilizadores. Esta solução não utiliza fichas de aplicação para autenticação.

![Relação entre utilizadores finais, Azure AD, e aplicações publicadas](./media/application-proxy-configure-native-client-application/richclientflow.png)

Para publicar aplicações nativas, utilize a Biblioteca de Autenticação da Microsoft, que cuida da autenticação e suporta muitos ambientes de clientes. O Proxy de aplicações encaixa-se [na aplicação Desktop que chama uma API web em nome de um](../develop/authentication-flows-app-scenarios.md#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user) cenário de utilizador inscrito.

Este artigo percorre os quatro passos para publicar uma aplicação nativa com Application Proxy e a Azure AD Authentication Library.

## <a name="step-1-publish-your-proxy-application"></a>Passo 1: Publique a sua aplicação de procuração

Publique a sua aplicação proxy como qualquer outra aplicação e atribua aos utilizadores para acederem à sua aplicação. Para obter mais informações, consulte [publicar aplicações com Application Proxy](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Passo 2: Registe a sua candidatura nativa

Tem agora de registar a sua candidatura na Azure AD, da seguinte forma:

1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com/). Aparece o **painel de instrumentos** do **centro de administração Azure Ative Directory.**
1. Na barra lateral, selecione **Azure Ative Directory**. Aparece a página geral **do Diretório Ativo Azure.**
1. Na barra lateral AZure AD, selecione **registos de Aplicações**. A lista de todos os registos de aplicações aparece.
1. Selecione **Novo registo**. Aparece a página **de inscrição do Registo.**

   ![Criar um novo registo de aplicações no portal Azure](./media/application-proxy-configure-native-client-application/create.png)

1. Na rubrica **Nome,** especifique um nome de visualização virado para o utilizador para a sua aplicação.
1. No título dos **tipos de conta suportados,** selecione um nível de acesso utilizando estas diretrizes:

   - Para direcionar apenas as contas que são internas para a sua organização, selecione **Contas apenas neste diretório organizacional.**
   - Para direcionar apenas clientes empresariais ou educativos, selecione **Contas em qualquer diretório organizacional.**
   - Para direcionar o conjunto mais amplo de identidades da Microsoft, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft.**
1. Em **Redirect URI**, selecione **Cliente Público (mobile & desktop)** e, em seguida, digite o URI de redirecionamento `https://login.microsoftonline.com/common/oauth2/nativeclient` para a sua aplicação.
1. Selecione e leia as Políticas da **Plataforma da Microsoft** e, em seguida, selecione **Registar**. É criada e exibida uma página geral para o novo registo de candidaturas.

Para obter informações mais detalhadas sobre a criação de um novo registo [de candidaturas, consulte a Integração de candidaturas com o Azure Ative Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Passo 3: Conceder acesso à sua aplicação de procuração

Agora que registou a sua aplicação nativa, pode dar-lhe acesso a outras aplicações no seu diretório, neste caso para aceder à aplicação proxy. Para permitir que a aplicação nativa seja exposta à aplicação proxy:

1. Na barra lateral da nova página de registo de candidaturas, selecione **permissões API**. Aparece a página **de permissões** da API para o novo registo de candidatura.
1. **Selecione Adicionar uma permissão**. Aparece a página **de permissões da API request.**
1. Sob a **definição de Seleção de API,** selecione **APIs que a minha organização utiliza**. Aparece uma lista que contém as aplicações no seu diretório que expõem AS.
1. Digite na caixa de pesquisa ou pergaminho para encontrar a aplicação de procuração que publicou no [Passo 1: Publique a sua aplicação de procuração](#step-1-publish-your-proxy-application)e, em seguida, selecione a aplicação proxy.
1. No **tipo de permissões que a sua aplicação requer?** Se a sua aplicação nativa necessitar de aceder à aplicação proxy API como utilizador inscrito, escolha **permissões delegadas**.
1. No título **de permissões Select,** selecione a permissão desejada e selecione **Permissões de adicionar**. A página **de permissões da API** para a sua aplicação nativa mostra agora a aplicação de procuração e a permissão API que adicionou.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>Passo 4: Adicione a Biblioteca de Autenticação da Microsoft ao seu código (amostra.NET C#)

Editar o código de aplicação nativo no contexto de autenticação da Biblioteca de Autenticação do Microsoft (MSAL) para incluir o seguinte texto: 

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

As informações necessárias no código de amostra podem ser encontradas no portal AD Azure, da seguinte forma:

| Informação necessária | Como encontrá-lo no portal AD AZure |
| --- | --- |
| \<Tenant ID> | Diretório Ativo **Azure**  >  **Propriedades**  >  **ID do diretório** |
| \<App ID of the Native app> | Inscrição de **pedido**  >  *sua aplicação*  >  nativa **Visão geral**  >  **ID de aplicação** |
| \<Scope> | Inscrição de **pedido**  >  *sua aplicação*  >  nativa **Permissões API** > Clique na API de Permissão (user_impersonation) > Um painel com a legenda **user_impersonation** aparece no lado direito. > O âmbito é o URL na caixa de edição.
| \<Proxy App Url> | o Url externo e o caminho para a API

Depois de editar o código MSAL com estes parâmetros, os seus utilizadores podem autenticar para aplicações de clientes nativos mesmo quando estão fora da rede corporativa.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o fluxo de aplicações nativas, consulte [aplicações nativas no Azure Ative Directory](../azuread-dev/native-app.md).

Saiba como configurar [o único súmis para as aplicações no Azure Ative Directory](sso-options.md#choosing-a-single-sign-on-method).