---
title: Configurar o serviço de Azure App de autenticação Azure Active Directory
description: Saiba como configurar a autenticação Azure Active Directory para seu aplicativo de serviços de aplicativos.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 41084c3532e29b3a52c121d48226c5a45857d5dc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088239"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Configurar seu aplicativo do serviço de aplicativo para usar Azure Active Directory entrar

> [!NOTE]
> Neste momento, o AAD v2 (incluindo MSAL) não tem suporte para serviços Azure App e Azure Functions. Verifique novamente se há atualizações.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar os serviços de Azure App para usar Azure Active Directory como um provedor de autenticação.

## <a name="express"> </a>Configurar com as configurações expressas

1. Na [Azure portal], navegue até o aplicativo do serviço de aplicativo. No painel de navegação esquerdo, selecione **autenticação/autorização**.
2. Se a **autenticação/autorização** não estiver habilitada, selecione **ativado**.
3. Selecione **Azure Active Directory**e, em seguida, selecione **expresso** em **modo de gerenciamento**.
4. Selecione **OK** para registrar o aplicativo do serviço de aplicativo em Azure Active Directory. Isso cria um novo registro de aplicativo. Se você quiser escolher um registro de aplicativo existente, clique em **selecionar um aplicativo existente** e procure o nome de um registro de aplicativo criado anteriormente em seu locatário.
   Clique no registro do aplicativo para selecioná-lo e clique em **OK**. Em seguida, clique em **OK** na página configurações de Azure Active Directory.
   Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.
5. Adicional Para restringir o acesso ao seu site somente para usuários autenticados pelo Azure Active Directory, defina **a ação a ser tomada quando a solicitação não for autenticada** para **fazer logon com Azure Active Directory**. Isso requer que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas para Azure Active Directory para autenticação.

> [!CAUTION]
> Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que querem um home page publicamente disponível, como em muitos aplicativos de página única. Para tais aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível, com o aplicativo iniciando o logon manualmente, conforme descrito [aqui](overview-authentication-authorization.md#authentication-flow).

6. Clique em **Guardar**.

## <a name="advanced"> </a>Definir com configurações avançadas

Você também pode fornecer as definições de configuração manualmente. Essa é a solução preferida se o locatário de Azure Active Directory que você deseja usar for diferente do locatário com o qual você entra no Azure. Para concluir a configuração, primeiro você deve criar um registro no Azure Active Directory e, em seguida, deve fornecer alguns dos detalhes de registro para o serviço de aplicativo.

### <a name="register"> </a>Registrar seu aplicativo do serviço de aplicativo com o Azure Active Directory

1. Entre no [Azure portal]e navegue até o aplicativo do serviço de aplicativo. Copie a **URL**do aplicativo. Você usará isso para configurar o registro do aplicativo Azure Active Directory.
2. Navegue até **Active Directory**, em seguida, selecione o **registros de aplicativo**, em seguida, clique em **novo registro de aplicativo** na parte superior para iniciar um novo registro de aplicativo. 
3. Na página **criar** , insira um **nome** para o registro do aplicativo, selecione o tipo de **aplicativo Web/API** , na caixa **URL de logon** , Cole a URL do aplicativo (da etapa 1). Em seguida, clique para **criar**.
4. Em alguns segundos, você deverá ver o novo registro de aplicativo que acabou de criar.
5. Depois que o registro do aplicativo tiver sido adicionado, clique no nome de registro do aplicativo, clique em **configurações** na parte superior e, em seguida, clique em **Propriedades** 
6. Na caixa **URI da ID do aplicativo** , Cole a URL do aplicativo (da etapa 1), também na **URL da Home Page** , cole na URL do aplicativo (da etapa 1) e, em seguida, clique em **salvar**
7. Agora, clique nas **URLs de resposta**, edite a **URL de resposta**, cole na URL do aplicativo (da etapa 1) e, em seguida, anexe-a ao final da URL, `https://contoso.azurewebsites.net/.auth/login/aad/callback` */.auth/login/AAD/callback* (por exemplo,). Clique em **Guardar**.

   > [!NOTE]
   > Você pode usar o mesmo registro de aplicativo para vários domínios adicionando **URLs de resposta**adicionais. Certifique-se de modelar cada instância do serviço de aplicativo com seu próprio registro, para que tenha suas próprias permissões e consentimento. Considere também o uso de registros de aplicativo separados para slots de site separados. Isso é para evitar que as permissões sejam compartilhadas entre ambientes, de modo que um bug no novo código que você está testando não afete a produção.
    
8. Neste ponto, copie a **ID do aplicativo** para o aplicativo. Mantenha-o para uso posterior. Você precisará dela para configurar seu aplicativo do serviço de aplicativo.
9. Feche a página do **aplicativo registrado** . Na página **registros de aplicativo** , clique no botão **pontos de extremidade** na parte superior e copie a URL do ponto de extremidade de **logon do WS-Federation** , mas remova `/wsfed` o final da URL. O resultado final deve ser semelhante `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`a. O nome de domínio pode ser diferente para uma nuvem soberanas. Isso servirá como a URL do emissor para mais tarde.

### <a name="secrets"> </a>Adicionar informações de Azure Active Directory ao seu aplicativo do serviço de aplicativo

1. De volta ao [Azure portal], navegue até o aplicativo do serviço de aplicativo. Clique em **autenticação/autorização**. Se o recurso de autenticação/autorização não estiver habilitado, ative a opção para **ativado**. Clique em **Azure Active Directory**, em provedores de autenticação, para configurar seu aplicativo.

    Adicional Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo. Defina a **ação a ser tomada quando a solicitação não for autenticada** para **fazer logon com Azure Active Directory**. Essa opção requer que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas para Azure Active Directory para autenticação.
2. Na configuração de autenticação do Active Directory, clique em **avançado** em **modo de gerenciamento**. Cole a ID do aplicativo na caixa ID do cliente (da etapa 8) e cole na URL (da etapa 9) no valor da URL do emissor. Em seguida, clique em **OK**.
3. Na página configuração de autenticação do Active Directory, clique em **salvar**.

Agora você está pronto para usar Azure Active Directory para autenticação em seu aplicativo do serviço de aplicativo.

## <a name="configure-a-native-client-application"></a>Configurar um aplicativo cliente nativo
Você pode registrar clientes nativos, o que fornece maior controle sobre o mapeamento de permissões. Você precisará disso se desejar executar entradas usando uma biblioteca de cliente, como a **biblioteca de autenticação do Active Directory**.

1. Navegue até **Azure Active Directory** na [Azure portal].
2. No painel de navegação esquerdo, selecione **registros de aplicativo**. Clique em **novo registro de aplicativo** na parte superior.
4. Na página **criar** , insira um **nome** para o registro do aplicativo. Selecione **nativo** no **tipo de aplicativo**.
5. Na caixa **URI** de redirecionamento, insira o ponto de extremidade */.auth/login/Done* do seu site, usando o esquema HTTPS. Esse valor deve ser semelhante a *https://contoso.azurewebsites.net/.auth/login/done* . Ao criar um aplicativo do Windows, use o [SID do pacote](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como o URI.
5. Clique em **Criar**.
6. Depois que o registro do aplicativo tiver sido adicionado, selecione-o para abri-lo. Localize a **ID do aplicativo** e anote esse valor.
7. Clique em **todas as configurações** > **permissões** > necessárias**Adicionar** > **selecionar uma API**.
8. Digite o nome do aplicativo do serviço de aplicativo que você registrou anteriormente para procurá-lo e, em seguida, selecione-o e clique em **selecionar**.
9. Selecione **Access \<app_name >** . Em seguida, clique em **Selecionar**. Em seguida, clique em **Guardar**.

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
