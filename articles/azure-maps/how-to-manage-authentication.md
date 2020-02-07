---
title: Gerenciar autenticação | Mapas do Microsoft Azure
description: Utilize o portal Azure para gerir a autenticação no Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 30eb637e9117818758ed4ab0e3adef9db29cc698
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057316"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerenciar a autenticação no Azure Maps

Depois de criar uma conta Azure Maps, é criado um ID e chaves para apoiar a autenticação do Azure Ative Directory (Azure AD) e a autenticação da Chave Partilhada.

## <a name="view-authentication-details"></a>Exibir detalhes de autenticação

Depois de criar uma conta Azure Maps, as chaves primárias e secundárias são geradas. Recomendamos que utilize uma chave primária como chave de subscrição quando utilizar a [autenticação de chave partilhada para ligar para o Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Pode utilizar uma chave secundária em cenários como mudanças de chave de rolamento. Para mais informações, consulte [Autenticação em Mapas Azure](https://aka.ms/amauth).

Pode ver os seus dados de autenticação no portal Azure. No menu **Definições,** selecione **Autenticação**.

![Detalhes da autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Registe e configure uma aplicação Azure AD

1. No portal Azure, na lista de serviços azure, selecione **registos** de > app do **Azure Ative Directory** > **Novo registo.**  

    ![Registo da aplicação](./media/how-to-manage-authentication/app-registration.png)

1. Se já registou a sua aplicação, continue até ao próximo passo. Se não tiver registado a sua aplicação, introduza um **Nome,** escolha um tipo de **conta de suporte**e, em seguida, selecione **Register**.  

    ![Detalhes de registro do aplicativo](./media/how-to-manage-authentication/app-create.png)

1. Para atribuir permissões api delegadas ao Azure Maps, vá ao pedido. Em seguida, sob **os registos da App,** selecione **permissões API** > **Adicionar uma permissão**. Sob **APIs a minha organização usa,** procure e selecione **Mapas Azure.**

    ![Adicionar permissões api app](./media/how-to-manage-authentication/app-permissions.png)

1. Selecione a caixa de verificação ao lado do **Access Azure Maps**e, em seguida, selecione **Adicionar permissões**.

    ![Selecionar permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

1. Complete um dos seguintes passos, dependendo do seu método de autenticação. 

    * Se a sua aplicação utilizar a autenticação de token do utilizador com o Azure Maps Web SDK, então ative `oauth2AllowImplicitFlow`. Para o permitir, na secção **Manifesto** do registo da sua aplicação, detete `oauth2AllowImplicitFlow` verdadeiramente. 
    
       ![Manifesto do aplicativo](./media/how-to-manage-authentication/app-manifest.png)

    * Se a sua aplicação utilizar a autenticação do servidor ou da aplicação, então na sua página de registo de aplicações, vá a **Certificados e segredos.** Em seguida, faça o upload de um certificado de chave pública ou crie uma palavra-passe selecionando **o novo segredo do cliente**. 
    
       ![Criar um segredo do cliente](./media/how-to-manage-authentication/app-keys.png)

        Se criar uma palavra-passe, depois de selecionar **Adicionar,** copie a palavra-passe e guarde-a de forma segura. Vais usar esta palavra-passe para obter fichas do Azure AD.

       ![Adicione um segredo de cliente](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Grant controlo de acesso baseado em papéis ao Azure Maps

Depois de associar uma conta Azure Maps ao seu inquilino Azure AD, pode conceder o controlo de acesso. Concede controlo *de acesso baseado em papéis* (RBAC) atribuindo um utilizador, grupo ou aplicação a uma ou mais funções de controlo de acesso do Azure Maps. 

1. Vá à sua **Conta Azure Maps**. Selecione controlo de **acesso (IAM)**  > Atribuição de **funções**.

    ![Conceder RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. No separador **de atribuições role,** em **Funções,** selecione **Azure Maps Date Reader (Pré-visualização)** . Sob **acesso de atribuição a**, selecione utilizador, grupo ou diretor de serviço **Azure AD**. Selecione o utilizador ou aplicação. Em seguida, selecione **Guardar**.

    ![Adicionar atribuição de papéis](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Exibir funções de RBAC do Azure Maps disponíveis

Para ver as funções RBAC disponíveis para o Azure Maps, aceda ao controlo de **acesso (IAM)** . Selecione **Funções**, e, em seguida, procure funções que comecem com *O Mapa Azul*. Estas funções do Azure Maps são os papéis a que pode conceder acesso.

![Exibir funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Exibir RBAC do Azure Maps

O RBAC fornece controle de acesso granular.

Para ver utilizadores e aplicações que tenham sido concedidos RBAC para o Azure Maps, vá ao **Access Control (IAM)** . Lá, selecione **atribuições de role,** e depois filtrar por **Azure Maps**.

![Ver utilizadores e aplicativos que tenham sido concedidos RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Tokens de solicitação para mapas do Azure

Depois de registar a sua aplicação e associá-la ao Azure Maps, pode solicitar fichas de acesso.

Se a sua aplicação utilizar a autenticação de token de utilizador com o Azure Maps Web SDK, configure a sua página HTML com o ID do cliente do Azure Maps e o ID da aplicação Azure AD.

Se a sua aplicação utilizar a autenticação do servidor ou da aplicação, solicite um token do ponto final do ponto final do AD Azure `https://login.microsoftonline.com`. A seu pedido, utilize os seguintes detalhes: 

* `https://atlas.microsoft.com/` de identificação de recursos da Azure AD
* ID do cliente do Azure Maps
* ID do aplicativo do Azure AD
* Senha ou certificado de registo de aplicativos Da Azure AD

| Ambiente azul   | Ponto de extremidade de token do Azure AD | ID de recurso Azure |
| --------------------|-------------------------|-------------------|
| Nuvem pública azul        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Nuvem do Governo de Azure   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Para obter mais informações sobre a solicitação de fichas de acesso da AD Azure para utilizadores e diretores de serviço, consulte cenários de [autenticação para a AD Azure](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [Azure AD e Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Encontre as métricas de utilização da API para a sua conta Azure Maps:
> [!div class="nextstepaction"] 
> [Ver métricas de utilização](how-to-view-api-usage.md)

Explore amostras que mostrem como integrar o Azure AD com o Azure Maps:

> [!div class="nextstepaction"]
> [Amostras de autenticação da AD Azure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
