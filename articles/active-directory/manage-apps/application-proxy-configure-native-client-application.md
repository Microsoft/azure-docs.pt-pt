---
title: Publicar aplicações de cliente nativo – Azure AD | Documentos da Microsoft
description: Aborda como permitir que as aplicações de cliente nativo comunicar com o Azure AD de conector de Proxy de aplicações para fornecer acesso remoto seguro às suas aplicações no local.
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
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb36d6a03da07681db468184a489a79f7f0deab7
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825493"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Como ativar aplicações de cliente nativo interagir com aplicativos de proxy

Pode utilizar o Proxy de aplicações do Azure Active Directory (Azure AD) para publicar aplicações web, mas ele também pode ser usado para publicar aplicações de cliente nativo que estão configuradas com o Azure AD Authentication Library (ADAL). Aplicações clientes nativas diferem das aplicações web, porque estão instaladas num dispositivo, enquanto as aplicações web são acedidas através de um browser.

Para suportar aplicações de cliente nativo, o Proxy de aplicações aceita tokens emitidos por AD do Azure que são enviadas no cabeçalho. O serviço de Proxy de aplicações faz a autenticação para os utilizadores. Esta solução não usa tokens de aplicação para autenticação.

![Relação entre os utilizadores finais, o Azure Active Directory e aplicações publicadas](./media/application-proxy-configure-native-client-application/richclientflow.png)

Para publicar aplicativos nativos, utilize a biblioteca de autenticação do AD do Azure, que se encarrega de autenticação e oferece suporte a vários ambientes de cliente. Proxy de aplicações se adapta a [aplicativo nativo para o cenário de Web API](../develop/native-app.md).

Este artigo descreve os quatro passos para publicar um aplicativo nativo com o Proxy de aplicações e a biblioteca de autenticação do Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Passo 1: Publicar a aplicação de proxy

Publicar a aplicação de proxy, tal como faria com qualquer outro aplicativo e atribuir utilizadores à sua aplicação. Para obter mais informações, consulte [publicar aplicações com o Proxy de aplicações](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Passo 2: Registar a sua aplicação nativa

Agora tem de registar a sua aplicação no Azure AD, da seguinte forma:

1. Inicie sessão para o [portal do Azure Active Directory](https://aad.portal.azure.com/). O **Dashboard** para o **Centro de administração do Azure Active Directory** aparece.
2. Na barra lateral, selecione **do Azure Active Directory**. O **do Azure Active Directory** é apresentada a página de descrição geral.
3. Na barra lateral descrição geral do Azure AD, selecione **registos das aplicações**. É apresentada a lista de todos os registos de aplicações.
4. Selecione **novo registo**. O **registar uma aplicação** é apresentada a página.

   ![Criar um novo registo de aplicação](./media/application-proxy-configure-native-client-application/create.png)
5. Na **nome** cabeçalho, especifique um nome a apresentar de destinada ao utilizador para a sua aplicação.
6. Sob o **tipos de conta suportados** de título, selecione um nível de acesso utilizando estas diretrizes:
   - Para filtrar apenas as contas que são internas para a sua organização, selecione **contas neste diretório organizacional apenas**.
   - Para filtrar apenas os negócios ou os clientes de instituições de ensino, selecione **contas em qualquer diretório organizacional**.
   - Para filtrar o conjunto mais ampla de identidades da Microsoft, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais**.
7. Na **URI de redirecionamento** cabeçalho, selecione **cliente público (móvel e ambiente de trabalho)**, e, em seguida, escreva o URI de redirecionamento para a sua aplicação.
8. Selecione e leia os **políticas de plataforma da Microsoft**e, em seguida, selecione **registar**. Uma página de descrição geral para o novo registo de aplicação é criada e apresentada.

Para obter mais informações sobre como criar um novo registo de aplicação, consulte [integrar aplicações com o Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Passo 3: Conceder acesso à sua aplicação de proxy

Agora que se registra seu aplicativo nativo, é possível dar acesso a outros aplicativos no seu diretório, nesse caso para aceder à aplicação de proxy. Para ativar o aplicativo nativo ser exposto para o proxy de aplicações:

1. Na barra lateral da nova página de registo de aplicação, selecione **permissões API**. O **permissões API** página para o novo registo de aplicação é apresentada.
2. Selecione **adicionar uma permissão**. O **permissões de pedido de API** é apresentada a página.
3. Sob o **selecionar uma API** definição, selecione **APIs minha organização utiliza**. É apresentada uma lista que contém as aplicações no seu diretório que expõem APIs.
4. Escreva na caixa de pesquisa ou desloque-se para encontrar o proxy de aplicações que publicou no [passo 1: Publicar a aplicação de proxy](#step-1-publish-your-proxy-application)e, em seguida, selecione a aplicação do proxy.
5. Na **o tipo de permissões de seu aplicativo requer?** de título, selecione o tipo de permissão. Se precisar de seu aplicativo nativo aceder à API de aplicação de proxy como o utilizador com sessão iniciada, escolha **permissões delegadas**. Se seu aplicativo nativo é executado como um serviço em segundo plano ou daemon sem um utilizador com sessão iniciada, escolha **permissões de aplicação**.
6. Na **selecionar permissões** cabeçalho, selecione a permissão pretendida e selecione **adicionar permissões**. O **permissões API** página para seu aplicativo nativo agora mostra o proxy de aplicação e permissão da API que foi adicionado.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Passo 4: Editar a biblioteca de autenticação do Active Directory

Edite o código de aplicação nativa no contexto de autenticação do Active Directory Authentication Library (ADAL) para incluir o seguinte texto:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

As informações necessárias no código de exemplo podem ser encontrada no portal do Azure AD, da seguinte forma:

| Informações necessárias | Como a encontrá-lo no portal do Azure AD |
| --- | --- |
| \<ID de inquilino > | **O Azure Active Directory** > **propriedades** > **ID de diretório** |
| \<Url externo de Proxy de aplicações > | **Aplicações empresariais** > *seu proxy de aplicações* > **proxy de aplicações** > **Url externo** |
| \<ID da aplicação da aplicação nativa > | **Aplicações empresariais** > *seu aplicativo nativo* > **propriedades** > **ID da aplicação** |
| \<URI da aplicação nativa de redirecionamento > | **O Azure Active Directory** > **registos das aplicações** > *seu aplicativo nativo* > **URIs de redirecionamento** |
| \<Url de API de aplicação do proxy > | **O Azure Active Directory** > **registos das aplicações** > *seu aplicativo nativo* > **permissões de API**  >  **API / nome de PERMISSÕES** |

Depois de editar a ADAL com estes parâmetros, os utilizadores podem autenticar para aplicações de cliente nativo, mesmo quando estão fora da rede empresarial.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o fluxo da aplicação nativa, consulte [aplicações nativas no Azure Active Directory](../develop/native-app.md).

Saiba mais sobre como configurar [início de sessão único para aplicações no Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
