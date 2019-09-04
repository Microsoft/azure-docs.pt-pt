---
title: Configurar o serviço de Azure App de autenticação Azure Active Directory
description: Saiba como configurar a autenticação Azure Active Directory para seu aplicativo do serviço de aplicativo.
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8b4b6549f9553773cc44c311f49befbb3eec9dc9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233092"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Configurar seu aplicativo do serviço de aplicativo para usar Azure Active Directory entrar

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> Neste momento, o AAD v2 (incluindo MSAL) não tem suporte para Azure App serviço e Azure Functions.
>

Este artigo mostra como configurar Azure App serviço para usar Azure Active Directory como um provedor de autenticação.

É recomendável que você configure cada aplicativo do serviço de aplicativo com seu próprio registro, portanto, ele tem suas próprias permissões e consentimento. Além disso, considere o uso de registros de aplicativo separados para slots de implantação separados. Isso evita o compartilhamento de permissões entre ambientes, de forma que um problema no novo código que você está testando não afete a produção.

## <a name="express"> </a>Configurar com as configurações expressas

1. Na [Azure portal], navegue até o aplicativo do serviço de aplicativo. No painel de navegação esquerdo, selecione **autenticação/autorização**.
2. Se a **autenticação/autorização** não estiver habilitada, selecione **ativado**.
3. Selecione **Azure Active Directory**e, em seguida, selecione **expresso** em **modo de gerenciamento**.
4. Selecione **OK** para registrar o aplicativo do serviço de aplicativo em Azure Active Directory. Isso cria um novo registro de aplicativo. Se você quiser escolher um registro de aplicativo existente, clique em **selecionar um aplicativo existente** e procure o nome de um registro de aplicativo criado anteriormente em seu locatário. Clique no registro do aplicativo para selecioná-lo e clique em **OK**. Em seguida, clique em **OK** na página configurações de Azure Active Directory.
Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.
5. Adicional Para restringir o acesso ao seu aplicativo somente aos usuários autenticados pelo Azure Active Directory, defina **a ação a ser tomada quando a solicitação não for autenticada** para **fazer logon com Azure Active Directory**. Isso requer que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas para Azure Active Directory para autenticação.

    > [!NOTE]
    > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que querem um home page publicamente disponível, como em muitos aplicativos de página única. Para tais aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível, com o aplicativo iniciando o logon manualmente, conforme descrito [aqui](overview-authentication-authorization.md#authentication-flow).
6. Clique em **Guardar**.

## <a name="advanced"> </a>Definir com configurações avançadas

Você também pode fornecer as definições de configuração manualmente, se o locatário de Azure Active Directory que você deseja usar for diferente do locatário com o qual você entra no Azure. Para concluir a configuração, primeiro você deve criar um registro no Azure Active Directory e, em seguida, deve fornecer alguns dos detalhes de registro para o serviço de aplicativo.

### <a name="register"> </a>Criar um registro de aplicativo no Azure ad para seu aplicativo do serviço de aplicativo

Ao criar um registro de aplicativo manualmente, observe três partes de informações que serão necessárias mais tarde ao configurar seu aplicativo do serviço de aplicativo: a ID do cliente, a ID do locatário e, opcionalmente, o segredo do cliente e o URI da ID do aplicativo.

1. No [Azure portal], navegue até o aplicativo do serviço de aplicativo e anote a **URL**do seu aplicativo. Você o usará para configurar o registro do aplicativo Azure Active Directory.
1. No [Azure portal], no menu à esquerda, selecione **Active Directory** > **registros de aplicativo** > **novo registro**. 
1. Na página **registrar um aplicativo** , insira um **nome** para o registro do aplicativo.
1. Em **URI**de redirecionamento, selecione **Web** e digite a URL do aplicativo do serviço de aplicativo `/.auth/login/aad/callback`e acrescente o caminho. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Em seguida, selecione **Criar**.
1. Depois que o registro do aplicativo for criado, copie a **ID do aplicativo (cliente)** e a **ID do diretório (locatário)** para mais tarde.
1. Selecione **identidade visual**. Na **URL da Home Page**, digite a URL do aplicativo do serviço de aplicativo e selecione **salvar**.
1. Selecione **expor um conjunto de API** > . Cole a URL do aplicativo do serviço de aplicativo e selecione **salvar**.

    > [!NOTE]
    > Esse valor é o **URI de ID do aplicativo** do registro do aplicativo. Se você quiser ter um aplicativo Web front-end para acessar uma API de back-end, por exemplo, e desejar que o back-end Conceda explicitamente acesso ao front-end, será necessário o **URI da ID do aplicativo** do *front-end* quando você configurar o recurso de aplicativo do serviço de aplicativo do < C2 > back-end.
1. Selecione **Adicionar âmbito**. Em **nome do escopo**, digite *user_impersonation*. Nas caixas de texto, digite o nome e a descrição do escopo de consentimento que você deseja que os usuários vejam na página de consentimento, como *acessar meu aplicativo*. Quando terminar, clique em **Adicionar escopo**.
1. Adicional Para criar um segredo do cliente, selecione **certificados & segredos** > **novo** > **Adicionar**segredo do cliente. Copie o valor do segredo do cliente mostrado na página. Depois de sair, ela não será mostrada novamente.
1. Adicional Para adicionar várias **URLs de resposta**, selecione **autenticação** no menu.

### <a name="secrets"> </a>Adicionar informações de Azure Active Directory ao seu aplicativo do serviço de aplicativo

1. Na [Azure portal], navegue até o aplicativo do serviço de aplicativo. No menu à esquerda, selecione **autenticação/autorização**. Se o recurso autenticação/autorização não estiver habilitado, selecione **ativado**. 
1. Adicional Por padrão, a autenticação do serviço de aplicativo permite o acesso não autenticado ao seu aplicativo. Para impor a autenticação de usuário, defina **a ação a ser tomada quando a solicitação não for autenticada** para **fazer logon com Azure Active Directory**.
1. Em provedores de autenticação, selecione **Azure Active Directory**.
1. Em **modo de gerenciamento**, selecione **avançado** e configure a autenticação do serviço de aplicativo de acordo com a tabela a seguir:

    |Campo|Descrição|
    |-|-|
    |ID de Cliente| Use a **ID do aplicativo (cliente)** do registro do aplicativo. |
    |ID do emissor| Use `https://login.microsoftonline.com/<tenant-id>`e substitua  *\<o ID do locatário >* com a **ID do diretório (locatário)** do registro do aplicativo. |
    |Segredo do cliente (opcional)| Use o segredo do cliente gerado no registro do aplicativo.|
    |Audiências de Token permitidas| Se este for um aplicativo de *back-end* e você quiser permitir tokens de autenticação de um aplicativo de front-end, adicione o **URI de ID do aplicativo** do front- *end* aqui. |
1. Selecione **OK**e, em seguida, selecione **salvar**.

Agora você está pronto para usar Azure Active Directory para autenticação em seu aplicativo do serviço de aplicativo.

## <a name="configure-a-native-client-application"></a>Configurar um aplicativo cliente nativo
Você pode registrar clientes nativos se desejar executar entradas usando uma biblioteca de cliente, como o **biblioteca de autenticação do Active Directory**.

1. No [Azure portal], no menu à esquerda, selecione **Active Directory** > **registros de aplicativo** > **novo registro**. 
1. Na página **registrar um aplicativo** , insira um **nome** para o registro do aplicativo.
1. Em **URI**de redirecionamento, selecione **cliente público (Mobile & Desktop)** e digite a URL do aplicativo do serviço de aplicativo `/.auth/login/aad/callback`e acrescente o caminho. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Em seguida, selecione **Criar**.

    > [!NOTE]
    > Para um aplicativo do Windows, use o [SID do pacote](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como o URI em vez disso.
1. Depois que o registro do aplicativo for criado, copie o valor da **ID do aplicativo (cliente)** .
1. No menu à esquerda, selecione **permissões** > de API**Adicionar uma permissão** > **minhas APIs**.
1. Selecione o registro de aplicativo que você criou anteriormente para seu aplicativo do serviço de aplicativo. Se você não vir o registro do aplicativo, verifique se adicionou o escopo **user_impersonation** em [criar um registro de aplicativo no Azure ad para seu aplicativo do serviço de aplicativo](#register).
1. Selecione **user_impersonation** e clique em **adicionar permissões**.

Agora você configurou um aplicativo cliente nativo que pode acessar seu aplicativo do serviço de aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[alternative method]:#advanced
