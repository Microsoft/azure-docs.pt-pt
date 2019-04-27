---
title: Publicar aplicações de cliente nativo – Azure AD | Documentos da Microsoft
description: Aborda como permitir que as aplicações de cliente nativo comunicar com o Azure AD de conector de Proxy de aplicações para fornecer acesso remoto seguro às suas aplicações no local.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21e101dee878a48cce1005d51ad5e59166b0cfa1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60293324"
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Como ativar aplicações de cliente nativo interagir com aplicativos de proxy

Além dos aplicativos da web, o Proxy de aplicações do Azure Active Directory também pode ser utilizado para publicar aplicações de cliente nativo que estão configuradas com o Azure AD Authentication Library (ADAL). Aplicações clientes nativas diferem das aplicações web, porque estão instaladas num dispositivo, enquanto as aplicações web são acedidas através de um browser. 

Proxy de aplicações suporta aplicações de cliente nativo ao aceitar do Azure AD enviados no cabeçalho de tokens emitido. O serviço de Proxy de aplicações efetua a autenticação em nome dos utilizadores. Esta solução não utiliza os tokens de aplicação para autenticação. 

![Relação entre os utilizadores finais, o Azure Active Directory e aplicações publicadas](./media/application-proxy-configure-native-client-application/richclientflow.png)

Utilize a biblioteca de autenticação do AD do Azure, que se encarrega de autenticação e oferece suporte a vários ambientes de cliente, para publicar aplicativos nativos. Proxy de aplicações se adapta a [aplicativo nativo para o cenário de Web API](../develop/native-app.md). 

Este artigo descreve os quatro passos para publicar um aplicativo nativo com o Proxy de aplicações e a biblioteca de autenticação do Azure AD. 

## <a name="step-1-publish-your-application"></a>Passo 1: Publicar a aplicação
Publicar a aplicação de proxy, tal como faria com qualquer outro aplicativo e atribuir utilizadores à sua aplicação. Para obter mais informações, consulte [publicar aplicações com o Proxy de aplicações](application-proxy-add-on-premises-application.md).

## <a name="step-2-configure-your-application"></a>Passo 2: Configurar a aplicação
Configure o seu aplicativo nativo da seguinte forma:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para **do Azure Active Directory** > **registos das aplicações**.
3. Selecione **Novo registo de aplicação**.
4. Especifique um nome para a sua aplicação, selecione **nativo** como o tipo de aplicativo e forneça o URI de redirecionamento para a sua aplicação. 

   ![Criar um novo registo de aplicação](./media/application-proxy-configure-native-client-application/create.png)
5. Selecione **Criar**.

Para obter mais informações sobre como criar um novo registo de aplicação, consulte [integrar aplicações com o Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).


## <a name="step-3-grant-access-to-other-applications"></a>Passo 3: Conceder acesso a outras aplicações
Permita que o aplicativo nativo para serem expostas a outras aplicações no seu diretório:

1. Ainda na **registos das aplicações**, selecione o novo aplicativo nativo que acabou de criar.
2. Selecione **permissões API**.
3. Selecione **adicionar uma permissão**.
4. Abra a primeira etapa **selecionar uma API**.
5. Use a barra de pesquisa para localizar a aplicação de Proxy de aplicações publicadas na primeira secção. Escolha essa aplicação, em seguida, clique em **selecione**. 

   ![Procure a aplicação de proxy](./media/application-proxy-configure-native-client-application/select_api.png)
6. Abra a segunda etapa **selecionar permissões**.
7. Utilize a caixa de verificação para conceder o acesso de aplicativo nativo para a sua aplicação de proxy, em seguida, clique em **selecione**.

   ![Conceder acesso a aplicações de proxy](./media/application-proxy-configure-native-client-application/select_perms.png)
8. Selecione **Done** (Concluído).


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

As variáveis no código de exemplo devem ser substituídas da seguinte forma:

* **ID do inquilino** podem ser encontrados no portal do Azure. Navegue para **do Azure Active Directory** > **propriedades** e copie o ID de diretório. 
* **URL externo** é o URL de front-end que introduziu no Proxy de aplicações. Para localizar este valor, navegue para o **proxy de aplicações** secção da aplicação de proxy.
* **ID da aplicação** da aplicação nativa pode ser encontrado no **propriedades** página do aplicativo nativo.
* **URI da aplicação nativa de redirecionamento** pode ser encontrado no **URIs de redirecionamento** página do aplicativo nativo.

Depois da ADAL é editada com estes parâmetros, os usuários devem ser capazes de autenticar para aplicações de cliente nativo, mesmo quando estão fora da rede empresarial. 

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o fluxo da aplicação nativa, consulte [aplicativo nativo para a web API](../develop/native-app.md)

Saiba mais sobre como configurar [início de sessão único para o Proxy de aplicações](what-is-single-sign-on.md#single- sign-on-methods)
