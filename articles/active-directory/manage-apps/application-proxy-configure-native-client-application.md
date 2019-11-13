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
ms.openlocfilehash: 2cbee6bfcca3ddb356abe9dceab2fca07c152b07
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961795"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Como habilitar aplicativos cliente nativos para interagir com aplicativos de proxy

Você pode usar o proxy de aplicativo Azure Active Directory (AD do Azure) para publicar aplicativos Web, mas ele também pode ser usado para publicar aplicativos cliente nativos configurados com a ADAL (biblioteca de autenticação do Azure AD). Os aplicativos cliente nativos diferem dos aplicativos Web porque estão instalados em um dispositivo, enquanto os aplicativos Web são acessados por meio de um navegador.

Para dar suporte a aplicativos cliente nativos, o proxy de aplicativo aceita tokens emitidos pelo Azure AD que são enviados no cabeçalho. O serviço de proxy de aplicativo faz a autenticação para os usuários. Essa solução não usa tokens de aplicativo para autenticação.

![Relação entre os usuários finais, o Azure AD e os aplicativos publicados](./media/application-proxy-configure-native-client-application/richclientflow.png)

Para publicar aplicativos nativos, use a biblioteca de autenticação do Azure AD, que cuida da autenticação e dá suporte a muitos ambientes de cliente. Proxy de aplicações se adapta a [aplicativo nativo para o cenário de Web API](../develop/native-app.md).

Este artigo descreve os quatro passos para publicar um aplicativo nativo com o Proxy de aplicações e a biblioteca de autenticação do Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Etapa 1: publicar seu aplicativo de proxy

Publicar a aplicação de proxy, tal como faria com qualquer outro aplicativo e atribuir utilizadores à sua aplicação. Para obter mais informações, consulte [publicar aplicações com o Proxy de aplicações](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Etapa 2: registrar seu aplicativo nativo

Agora você precisa registrar seu aplicativo no Azure AD, da seguinte maneira:

1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com/). O **painel** para o **centro de administração do Azure Active Directory** é exibido.
1. Na barra lateral, selecione **Azure Active Directory**. A página Visão geral do **Azure Active Directory** é exibida.
1. Na barra lateral visão geral do Azure AD, selecione **registros de aplicativo**. A lista de todos os registros de aplicativo é exibida.
1. Selecione **novo registro**. A página **registrar um aplicativo** é exibida.

   ![Criar um novo registro de aplicativo no portal do Azure](./media/application-proxy-configure-native-client-application/create.png)

1. No título **nome** , especifique um nome de exibição voltado para o usuário para seu aplicativo.
1. No título **tipos de conta com suporte** , selecione um nível de acesso usando estas diretrizes:

   - Para direcionar apenas contas que são internas à sua organização, selecione **contas somente neste diretório organizacional**.
   - Para direcionar apenas clientes corporativos ou educacionais, selecione **contas em qualquer diretório organizacional**.
   - Para direcionar o conjunto mais amplo de identidades da Microsoft, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft**.

1. No cabeçalho **URI de redirecionamento** , selecione **cliente público (Mobile & Desktop)** e digite o URI de redirecionamento para seu aplicativo.
1. Selecione e leia as **políticas da plataforma Microsoft**e, em seguida, selecione **registrar**. Uma página de visão geral para o novo registro de aplicativo é criada e exibida.

Para obter informações mais detalhadas sobre como criar um novo registro de aplicativo, consulte [integrando aplicativos com Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Etapa 3: conceder acesso ao seu aplicativo de proxy

Agora que você registrou seu aplicativo nativo, você pode conceder a ele acesso a outros aplicativos em seu diretório, nesse caso, para acessar o aplicativo proxy. Para habilitar o aplicativo nativo a ser exposto ao aplicativo proxy:

1. Na barra lateral da página novo registro de aplicativo, selecione **permissões de API**. A página **permissões de API** para o novo registro de aplicativo é exibida.
1. Selecione **adicionar uma permissão**. A página **solicitar permissões de API** é exibida.
1. Na configuração **selecionar uma API** , selecione **APIs que minha organização usa**. Uma lista é exibida, contendo os aplicativos em seu diretório que expõem APIs.
1. Digite na caixa de pesquisa ou role para encontrar o aplicativo de proxy que você publicou na [etapa 1: publicar seu aplicativo proxy](#step-1-publish-your-proxy-application)e, em seguida, selecione o aplicativo proxy.
1. No cabeçalho **que tipo de permissões seu aplicativo exige?** , selecione o tipo de permissão. Se seu aplicativo nativo precisar acessar a API do aplicativo de proxy como o usuário conectado, escolha **permissões delegadas**.
1. No cabeçalho **selecionar permissões** , selecione a permissão desejada e selecione **adicionar permissões**. A página **permissões de API** para seu aplicativo nativo agora mostra o aplicativo proxy e a API de permissão que você adicionou.

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

As informações necessárias no código de exemplo podem ser encontradas no portal do AD do Azure, da seguinte maneira:

| Informações necessárias | Como encontrá-lo no portal do AD do Azure |
| --- | --- |
| > \<ID do locatário | **Propriedades** de > de **Azure Active Directory** > **ID de diretório** |
| \<URL externa do aplicativo proxy > | **Aplicativos empresariais** > *seu aplicativo proxy* > **proxy de aplicativo** > **URL externa** |
| \<a ID do aplicativo nativo > | **Aplicativos empresariais** > *seu aplicativo nativo* > **Propriedades** > **ID do aplicativo** |
| \<URI de redirecionamento do aplicativo nativo > | **Azure Active Directory** > **registros de aplicativo** > *seu aplicativo nativo* > **URIs de redirecionamento** |
| \<URL da API do aplicativo proxy > | **Azure Active Directory** > **registros de aplicativo** > *seu aplicativo nativo* > **permissões de API** > **nome da API/permissões** |

Depois de editar a ADAL com esses parâmetros, os usuários poderão se autenticar em aplicativos cliente nativos mesmo quando estiverem fora da rede corporativa.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o fluxo de aplicativo nativo, consulte [aplicativos nativos no Azure Active Directory](../develop/native-app.md).

Saiba mais sobre como configurar o [logon único para aplicativos no Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
