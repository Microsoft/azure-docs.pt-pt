---
title: Gerir a autenticação no Azure Maps | Documentos da Microsoft
description: Pode utilizar o portal do Azure para gerir a autenticação no Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 414ca7156fd59ec1dc08e45c73e9eb30835078d4
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259929"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerir a autenticação no Azure Maps

Depois de criar uma conta do Azure Maps, um ID de cliente e as chaves são criadas para suportar o Azure Active Directory (Azure AD) ou autenticação de chave partilhada.

## <a name="view-authentication-details"></a>Ver detalhes de autenticação

Pode ver os detalhes de autenticação no portal do Azure. Aceda à sua conta e selecione **autenticação** sobre o **definições** menu.

![Detalhes de autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)

 Para obter mais informações, consulte [autenticação com o Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Configurar o registo de aplicação do Azure AD

Depois de criar uma conta do Azure Maps, tem de estabelecer uma ligação entre o seu inquilino do Azure AD e o recurso do Azure Maps.

1. Aceda ao painel do Azure AD e crie um registo de aplicações. Forneça um nome para o registo. Na **URL de início de sessão** caixa, forneça a home page da aplicação web / API (por exemplo, https:\//localhost/). Se já tiver um aplicativo registrado, vá para o passo 2.

    ![Registo de aplicações](./media/how-to-manage-authentication/app-registration.png)

    ![Detalhes de registo de aplicação](./media/how-to-manage-authentication/app-create.png)

2. Para atribuir permissões delegadas de API para o Azure Maps, vá para o aplicativo sob **registos de aplicações**e, em seguida, selecione **definições**.  Selecione **permissões obrigatórias**e, em seguida, selecione **Add**. Procure e selecione **do Azure Maps** sob **selecionar uma API**e, em seguida, selecione o **selecionar** botão.

    ![Permissões de aplicações API](./media/how-to-manage-authentication/app-permissions.png)

3. Sob **selecionar permissões**, selecione **acesso do Azure Maps**e, em seguida, selecione o **selecionar** botão.

    ![Selecione as permissões de aplicações API](./media/how-to-manage-authentication/select-app-permissions.png)

4. Passo concluído um ou b, dependendo do seu método de autenticação.

    1. Se a sua aplicação utilizar a autenticação de token de utilizador com o SDK de Web do Azure Maps, ative `oauthEnableImplicitFlow` ao defini-la como true na secção manifesto da página de detalhes do registo da aplicação.
    
       ![Manifesto da aplicação](./media/how-to-manage-authentication/app-manifest.png)

    2. Se a sua aplicação utiliza a autenticação de servidor/aplicação, vá para o **chaves** painel no registo de aplicações e criar uma palavra-passe ou carregar um certificado de chave pública para o registo de aplicações. Se criar uma palavra-passe, depois de selecionar **guardar**, copie a palavra-passe para utilizar mais tarde e armazena de forma segura. Vai utilizar esta palavra-passe para adquirir tokens do Azure AD.

       ![Chaves da aplicação](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>RBAC de concessão para o Azure Maps

Depois de associar uma conta do Azure Maps inquilino do Azure AD, pode conceder controlo de acesso ao atribuir um utilizador ou aplicação para uma ou mais funções de controlo de acesso do Azure Maps.

1. Aceda a **controlo de acesso (IAM)**, selecione **atribuições de funções**e, em seguida, selecione **adicionar atribuição de função**.

    ![RBAC de concessão](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na **adicionar atribuição de função** janela, em **função**, selecione **leitor de data de mapas do Azure (pré-visualização)**. Sob **atribuir acesso aos**, selecione **utilizador, grupo ou principal de serviço do Azure AD**. Sob **selecione**, selecione o utilizador ou aplicação. Selecione **Guardar**.

    ![Adicionar atribuição de função](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Ver as funções de RBAC do Azure Maps disponíveis

Para ver as funções de controlo (RBAC) de acesso baseado em funções que estão disponíveis para o Azure Maps, aceda ao **controlo de acesso (IAM)**, selecione **funções**, e, em seguida, procure funções a partir do **doAzureMaps**. Estas são as funções que pode conceder acesso a.

![Modo de exibição de funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Ver mapas do Azure RBAC

RBAC fornece controlo de acesso granular.

Para ver os utilizadores e aplicações que tenham sido concedidas RBAC para o Azure Maps, aceda a **controlo de acesso (IAM)**, selecione **atribuições de funções**e, em seguida, filtrar por **Azure Maps**.

![Ver utilizadores e aplicações concedido RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Pedir tokens para o Azure Maps

Depois de registar a sua aplicação e associou-o com o Azure Maps, pode solicitar tokens de acesso.

* Se a sua aplicação utilizar a autenticação de token de utilizador com o SDK do Azure Maps Web, tem de configurar a sua página HTML com o ID de cliente do Azure Maps e o ID da aplicação do Azure AD.

* Se a aplicação utiliza a autenticação de servidor/aplicação, terá de solicitar um token a partir do ponto final de início de sessão do Azure AD `https://login.microsoftonline.com` com o ID de recurso do Azure AD `https://atlas.microsoft.com/`, o ID de cliente do Azure Maps, o ID da aplicação do Azure AD e o registo de aplicações do Azure AD palavra-passe ou certificado.

Para obter mais informações sobre o pedido de tokens de acesso do Azure AD para utilizadores e os principais de serviço, consulte [cenários de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre a autenticação do Azure AD e o SDK de Web de mapas do Azure, veja [do Azure AD e o SDK Web do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).