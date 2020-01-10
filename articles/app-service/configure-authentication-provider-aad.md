---
title: Configurar a autenticação do Azure AD
description: Saiba como configurar a autenticação Azure Active Directory como um provedor de identidade para seu aplicativo do serviço de aplicativo.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b85c68b19a44021710dbc9143e255600b43b2cba
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666147"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Configurar seu aplicativo do serviço de aplicativo para usar o logon do Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar Azure App serviço para usar o Azure Active Directory (AD do Azure) como um provedor de autenticação.

> [!NOTE]
> Neste momento, Azure App serviço e Azure Functions só têm suporte do Azure AD v 1.0. Eles não têm suporte da [plataforma Microsoft Identity v 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-overview), que inclui MSAL (bibliotecas de autenticação da Microsoft).

Siga estas práticas recomendadas ao configurar seu aplicativo e a autenticação:

- Dê a cada aplicativo do serviço de aplicativo suas próprias permissões e consentimento.
- Configure cada aplicativo do serviço de aplicativo com seu próprio registro.
- Evite o compartilhamento de permissão entre ambientes usando registros de aplicativo separados para slots de implantação separados. Ao testar o novo código, essa prática pode ajudar a evitar problemas de afetar o aplicativo de produção.

## <a name="express"> </a>Configurar com as configurações expressas

1. No [portal do Azure], procure e selecione serviços de **aplicativos**e, em seguida, selecione seu aplicativo.
1. No painel esquerdo, em **configurações** , selecione **autenticação/autorização** e verifique se a **autenticação do serviço de aplicativo** está **ativada**.
1. Selecione **Azure Active Directory**e, em **modo de gerenciamento** , selecione **expresso**.
1. Selecione **OK** para registrar o aplicativo do serviço de aplicativo em Azure Active Directory. Um novo registro de aplicativo é criado.

   Se você quiser escolher um registro de aplicativo existente, em vez disso:

   1. Escolha **selecionar um aplicativo existente** e procure o nome de um registro de aplicativo criado anteriormente em seu locatário.
   1. Selecione o registro do aplicativo e, em seguida, selecione **OK**.
   1. Em seguida, selecione **OK** na página configurações de Azure Active Directory.

   Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.
1. Adicional Para restringir o acesso do aplicativo somente aos usuários autenticados pelo Azure Active Directory, defina **a ação a ser tomada quando a solicitação não for autenticada** para **fazer logon com Azure Active Directory**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as Azure Active Directory não autenticadas para autenticação.

    > [!CAUTION]
    > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que têm um home page publicamente disponível, como em muitos aplicativos de página única. Para tais aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível, com o aplicativo iniciando o logon manualmente. Para obter mais informações, consulte [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).
1. Selecione **Guardar**.

## <a name="advanced"> </a>Definir com configurações avançadas

Você pode definir as configurações do aplicativo manualmente se quiser usar um locatário do AD do Azure que seja diferente daquele usado para entrar no Azure. Para concluir essa configuração personalizada, você precisará:

1. Crie um registro no Azure AD.
1. Forneça alguns dos detalhes de registro para o serviço de aplicativo.

### <a name="register"> </a>Criar um registro de aplicativo no Azure ad para seu aplicativo do serviço de aplicativo

Você precisará das seguintes informações ao configurar seu aplicativo do serviço de aplicativo:

- ID de Cliente
- ID do inquilino
- Segredo do cliente (opcional)
- URI da ID do aplicativo

Execute as seguintes etapas:

1. Entre no [portal do Azure], procure e selecione **serviços de aplicativos**e, em seguida, selecione seu aplicativo. Anote a **URL**do seu aplicativo. Você o usará para configurar o registro do aplicativo Azure Active Directory.
1. Selecione **Azure Active Directory** > **registros de aplicativo** > **novo registro**.
1. Na página **registrar um aplicativo** , insira um **nome** para o registro do aplicativo.
1. Em **URI de redirecionamento**, selecione **Web** e insira a URL do seu aplicativo do serviço de aplicativo e acrescente o caminho `/.auth/login/aad/callback`. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Selecione **Criar**.
1. Depois que o registro do aplicativo for criado, copie a **ID do aplicativo (cliente)** e a **ID do diretório (locatário)** para mais tarde.
1. Selecione **identidade visual**. Na **URL da Home Page**, insira a URL do seu aplicativo do serviço de aplicativo e selecione **salvar**.
1. Selecione **expor um conjunto de > de API** . Cole a URL do aplicativo do serviço de aplicativo e selecione **salvar**.

   > [!NOTE]
   > Esse valor é o **URI de ID do aplicativo** do registro do aplicativo. Se seu aplicativo Web exigir acesso a uma API na nuvem, você precisará do **URI de ID de aplicativo** do aplicativo Web ao configurar o recurso de serviço de aplicativo de nuvem. Você pode usar isso, por exemplo, se desejar que o serviço de nuvem Conceda explicitamente acesso ao aplicativo Web.

1. Selecione **Adicionar âmbito**.
   1. Em **nome do escopo**, insira *user_impersonation*.
   1. Nas caixas de texto, insira o nome do escopo de consentimento e a descrição que você deseja que os usuários vejam na página de consentimento. Por exemplo, digite *acessar meu aplicativo*. 
   1. Selecione **Adicionar escopo**.
1. Adicional Para criar um segredo do cliente, selecione **certificados & segredos** > **novo segredo do cliente** > **Adicionar**. Copie o valor do segredo do cliente mostrado na página. Ele não será mostrado novamente.
1. Adicional Para adicionar várias **URLs de resposta**, selecione **autenticação**.

### <a name="secrets"> </a>Adicionar informações de Azure Active Directory ao seu aplicativo do serviço de aplicativo

1. No [portal do Azure], procure e selecione serviços de **aplicativos**e, em seguida, selecione seu aplicativo. 
1. No painel esquerdo, em **configurações**, selecione **autenticação/autorização** e verifique se a **autenticação do serviço de aplicativo** está **ativada**.
1. Adicional Por padrão, a autenticação do serviço de aplicativo permite o acesso não autenticado ao seu aplicativo. Para impor a autenticação de usuário, defina **a ação a ser tomada quando a solicitação não for autenticada** para **fazer logon com Azure Active Directory**.
1. Em provedores de autenticação, selecione **Azure Active Directory**.
1. Em **modo de gerenciamento**, selecione **avançado** e configure a autenticação do serviço de aplicativo de acordo com a tabela a seguir:

    |Campo|Descrição|
    |-|-|
    |ID de Cliente| Use a **ID do aplicativo (cliente)** do registro do aplicativo. |
    |ID do emissor| Use `https://login.microsoftonline.com/<tenant-id>`e substitua *\<> ID do locatário* pela ID do **diretório (locatário)** do registro do aplicativo. |
    |Segredo do cliente (opcional)| Use o segredo do cliente gerado no registro do aplicativo.|
    |Públicos de token permitidos| Se este for um aplicativo de nuvem ou de servidor e você quiser permitir tokens de autenticação de um aplicativo Web, adicione o **URI de ID de aplicativo** do aplicativo Web aqui. |

    > [!NOTE]
    > A **ID do cliente** configurada *sempre* é considerada implicitamente como um público permitido, independentemente de como você configurou os **públicos de token permitidos**.
1. Selecione **OK**e, em seguida, selecione **salvar**.

Agora você está pronto para usar Azure Active Directory para autenticação em seu aplicativo do serviço de aplicativo.

## <a name="configure-a-native-client-application"></a>Configurar um aplicativo cliente nativo

Você pode registrar clientes nativos para permitir a autenticação usando uma biblioteca de cliente, como o **biblioteca de autenticação do Active Directory**.

1. Na [portal do Azure], selecione **Active Directory** > **registros de aplicativo** > **novo registro**.
1. Na página **registrar um aplicativo** , insira um **nome** para o registro do aplicativo.
1. Em **URI de redirecionamento**, selecione **cliente público (Mobile & Desktop)** e insira a URL do seu aplicativo do serviço de aplicativo e acrescente o caminho `/.auth/login/aad/callback`. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Selecione **Criar**.

    > [!NOTE]
    > Para um aplicativo do Windows, use o [SID do pacote](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como o URI em vez disso.
1. Depois que o registro do aplicativo for criado, copie o valor da **ID do aplicativo (cliente)** .
1. Selecione **permissões de API** > **adicionar uma permissão** > **minhas APIs**.
1. Selecione o registro de aplicativo que você criou anteriormente para seu aplicativo do serviço de aplicativo. Se você não vir o registro do aplicativo, certifique-se de ter adicionado o escopo de **user_impersonation** em [criar um registro de aplicativo no Azure ad para seu aplicativo do serviço de aplicativo](#register).
1. Selecione **user_impersonation**e, em seguida, selecione **adicionar permissões**.

Agora você configurou um aplicativo cliente nativo que pode acessar seu aplicativo do serviço de aplicativo.

## <a name="related-content"> </a>Próximas etapas

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

[Portal do Azure]: https://portal.azure.com/
[alternative method]:#advanced
