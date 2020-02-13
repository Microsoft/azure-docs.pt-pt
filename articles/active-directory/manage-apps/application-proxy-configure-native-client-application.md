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
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159289"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Como permitir que as aplicações de clientes nativos interajam com aplicações de procuração

Pode utilizar o Azure Ative Directory (Azure AD) Application Proxy para publicar aplicações web, mas também pode ser usado para publicar aplicações de clientes nativos que estão configuradas com a Biblioteca de Autenticação AD Azure (ADAL). As aplicações de clientes nativos diferem das aplicações web porque estão instaladas num dispositivo, enquanto as aplicações web são acedidas através de um navegador.

Para apoiar as aplicações de clientes nativos, a Application Proxy aceita fichas emitidas pela Azure AD que são enviadas no cabeçalho. O serviço Proxy de Aplicação faz a autenticação para os utilizadores. Esta solução não utiliza fichas de aplicação para autenticação.

![Relação entre utilizadores finais, Azure AD, e aplicações publicadas](./media/application-proxy-configure-native-client-application/richclientflow.png)

Para publicar aplicações nativas, utilize a Biblioteca de Autenticação AD Azure, que cuida da autenticação e suporta muitos ambientes de clientes. Application Proxy enquadra-se na aplicação nativa para o [cenário da Web API](../azuread-dev/native-app.md).

Este artigo descreve os quatro passos para publicar um aplicativo nativo com o Proxy de aplicações e a biblioteca de autenticação do Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Passo 1: Publique a sua aplicação proxy

Publicar a aplicação de proxy, tal como faria com qualquer outro aplicativo e atribuir utilizadores à sua aplicação. Para mais informações, consulte [Publicar aplicações com Procuração de Aplicações](application-proxy-add-on-premises-application.md).

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

1. Na rubrica Redirect URI, selecione **cliente Público (mobile & desktop)** e, em seguida, digite o URI **redirecionamento** para a sua aplicação.
1. Selecione e leia as Políticas da **Plataforma Microsoft,** e, em seguida, selecione **Register**. É criada e apresentada uma página geral para o novo registo de candidaturas.

Para obter informações mais detalhadas sobre a criação de um novo registo de candidaturas, consulte a Integração de [aplicações com o Diretório Ativo do Azure.](../develop/quickstart-register-app.md)

## <a name="step-3-grant-access-to-your-proxy-application"></a>Passo 3: Conceder acesso ao seu pedido de procuração

Agora que registou a sua aplicação nativa, pode dar-lhe acesso a outras aplicações no seu diretório, neste caso para aceder à aplicação de procuração. Para permitir que a aplicação nativa seja exposta à aplicação por procuração:

1. Na barra lateral da nova página de registo de candidaturas, selecione **permissões API**. Aparece a página de **permissões** da API para o novo registo de candidaturas.
1. Selecione **Adicionar uma permissão**. Aparece a página de **permissões da API request.**
1. Sob a definição **Select a API,** selecione **APIs que a minha organização utiliza**. Aparece uma lista, contendo as aplicações no seu diretório que expõem APIs.
1. Digite na caixa de pesquisa ou percorra para encontrar a aplicação proxy que publicou no Passo 1: Publique a [sua aplicação proxy](#step-1-publish-your-proxy-application), e, em seguida, selecione a aplicação proxy.
1. No **tipo de permissões que o seu pedido requer?** Se a sua aplicação nativa necessitar de aceder à aplicação proxy API como utilizador inscrito, escolha **permissões delegadas**.
1. Na rubrica **'Selecionar permissões',** selecione a permissão desejada e selecione **Adicionar permissões**. A página de **permissões da API** para a sua aplicação nativa mostra agora o pedido de procuração e a permissão API que adicionou.

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

As informações necessárias no código da amostra podem ser encontradas no portal Azure AD, da seguinte forma:

| Informações necessárias | Como encontrá-lo no portal Azure AD |
| --- | --- |
| \<Tenant ID> | **Azure Ative Directory** > **Propriedades** > **D.D.** |
| \<Url Externo da Proxy App> | **Aplicações empresariais** > *a sua aplicação de procuração* > procuração de **aplicação** > **Url Externo** |
| \<App ID da app nativa> | **Aplicações empresariais** > *sua aplicação nativa* > **Propriedades** > Id de **aplicação** |
| \<Redirecionar URI da App Nativa> | Registos de **aplicações** de > de **diretório ativo da Azure** > *a sua aplicação nativa* > **Redirecionamento de URIs** |
| \<App Proxy API Url> | **Registos** de > de **Diretório Ativo azure** > *sua aplicação nativa* > **permissões DaPI** > **Nome API/PERMISSÕES** |

Depois de editar o ADAL com estes parâmetros, os seus utilizadores podem autenticar aplicações de clientes nativos mesmo quando estão fora da rede corporativa.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o fluxo de aplicações nativas, consulte [aplicações nativas no Diretório Ativo Azure.](../azuread-dev/native-app.md)

Saiba mais sobre a criação de um [único sign-on para aplicações no Diretório Ativo Azure](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
