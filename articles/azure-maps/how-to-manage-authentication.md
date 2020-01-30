---
title: Gerenciar autenticação | Mapas do Microsoft Azure
description: Você pode usar o portal do Azure para gerenciar a autenticação no Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1f7f128898089292a8ccd92686af5d68fe328f3c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766112"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerenciar a autenticação no Azure Maps

Depois de criar uma conta Azure Maps, é criado um ID e chaves para suportar o Diretório Ativo Azure (Azure AD) e a autenticação de Chave Partilhada.

## <a name="view-authentication-details"></a>Exibir detalhes de autenticação

Após a criação da conta do Azure Maps, as chaves primária e secundária são geradas. Utilize a chave principal como chave de subscrição, às vezes estes nomes são usados intercambiavelmente. A chave secundária pode ser utilizada em cenários como mudanças de chave de rolamento. De qualquer forma, uma chave é necessária para ligar para o Azure Maps. Este processo chama-se [autenticação de chaves partilhadas.](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication) Consulte a [Autenticação com o Azure Maps](https://aka.ms/amauth) para saber mais sobre a autenticação da Chave Partilhada e da Autenticação AD Azure.

Você pode exibir os detalhes de autenticação no portal do Azure. Vá para sua conta e selecione **autenticação** no menu **configurações** .

![Detalhes da autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="set-up-azure-ad-app-registration"></a>Configurar o registro de aplicativo do Azure AD

Depois de criar uma conta do Azure Maps, você precisa estabelecer um vínculo entre o locatário do Azure AD e o recurso do Azure Maps.

1. Selecione **Azure Ative Diretório** a partir do menu do portal. Forneça um nome para o registro. Clique nas **inscrições da App** e clique em **Nova inscrição**. Na caixa **Redirect URI,** forneça a página inicial da aplicação web. Por exemplo, https://localhost/. Se você já tiver um aplicativo registrado, vá para a etapa 2.

    ![Registo da aplicação](./media/how-to-manage-authentication/app-registration.png)

    ![Detalhes de registro do aplicativo](./media/how-to-manage-authentication/app-create.png)

2. Para atribuir permissões api delegadas ao Azure Maps, vá à aplicação sob registos de **Aplicações**e, em seguida, selecione **permissões API**. **Selecione Adicionar Permissão**. Procure e selecione **Mapas Azure** sob **selecione um API**.

    ![Permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

3. Em **'Selecionar permissões',** verifique a caixa para **obter a personificação**do utilizador e, em seguida, clique no botão **Select** na parte inferior.

    ![Selecionar permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

4. Conclua a etapa a ou b, dependendo do seu método de autenticação.

    1. Se a sua aplicação utilizar a autenticação de token do utilizador com o Azure Maps Web SDK, ative `oauth2AllowImplicitFlow` definindo-a de forma verdadeira na secção Manifesto do registo da sua aplicação.
    
       ![Manifesto do aplicativo](./media/how-to-manage-authentication/app-manifest.png)

    2. Se a sua aplicação utilizar a autenticação do servidor/aplicação, aceda à lâmina de **Certificados e Segredos** no registo da aplicação e crie uma palavra-passe ou faça upload de um certificado de chave pública para o registo da aplicação. Se criar uma palavra-passe, guarde-a de forma segura para posterior utilização. Você usará essa senha para adquirir tokens do Azure AD.

       ![Chaves de aplicativo](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>Grant controlo de acesso baseado em papéis (RBAC) para O Mapa Azure

Depois de associar uma conta Azure Maps ao seu inquilino Azure AD, pode conceder o controlo de acesso. Concede o controlo de acesso atribuindo um utilizador, grupo ou aplicação a uma ou mais funções de controlo de acesso do Azure Maps.

1. Vá à sua **Conta Azure Maps**. Selecione **o controlo de acesso (IAM)** e, em seguida, selecione a atribuição de **funções**.

    ![Conceder RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na janela de atribuição de **funções,** em **função da Função,** **selecione Azure Maps Date Reader (Pré-visualização)** . Sob **a atribuição de acesso para** selecionar o princípio do **utilizador, grupo ou serviço Azure AD**. Selecione o utilizador ou aplicação. Selecione **Guardar**.

    ![Adicionar atribuição de papéis](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Exibir funções de RBAC do Azure Maps disponíveis

Para exibir funções RBAC (controle de acesso baseado em função) que estão disponíveis para mapas do Azure, vá para **controle de acesso (iam)** , selecione **funções**e, em seguida, pesquise funções começando com o **Azure Maps**. Estes papéis são os papéis a que se pode dar acesso.

![Exibir funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Exibir RBAC do Azure Maps

O RBAC fornece controle de acesso granular.

Para exibir usuários e aplicativos que receberam o RBAC para mapas do Azure, vá para **controle de acesso (iam)** , selecione **atribuições de função**e filtre por **mapas do Azure**.

![Exibir usuários e aplicativos com o RBAC concedido](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Tokens de solicitação para mapas do Azure

Depois de registrar seu aplicativo e associo-lo ao Azure Maps, você pode solicitar tokens de acesso.

* Se seu aplicativo usar a autenticação de token de usuário com o SDK da Web do Azure Maps, você precisará configurar sua página HTML com a ID do cliente do Azure Maps e a ID do aplicativo do Azure AD.

* Se seu aplicativo usar a autenticação de servidor/aplicativo, você precisará solicitar um token do ponto de extremidade de token do Azure AD `https://login.microsoftonline.com` com a ID de recurso do Azure AD `https://atlas.microsoft.com/`, a ID do cliente do Azure Maps, a ID do aplicativo do Azure AD e a senha ou o certificado de registro do aplicativo do Azure AD.

| Ambiente do Azure   | Ponto de extremidade de token do Azure AD | ID de recurso do Azure |
| --------------------|-------------------------|-------------------|
| Azure Public        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Para obter mais informações sobre a solicitação de fichas de acesso da Azure AD, para utilizadores e diretores de serviço, consulte cenários de [autenticação para a AD Azure](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a autenticação do Azure AD e o SDK da Web do Azure Maps, consulte SDK para Web do Azure [AD e Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Saiba como ver as métricas de uso da API para sua conta do Azure Maps:
> [!div class="nextstepaction"] 
> [Exibir métricas de uso](how-to-view-api-usage.md)

Para obter uma lista de exemplos que mostram como integrar o Azure Active Directory (AAD) com o Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Exemplos de autenticação do Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
