---
title: Autenticar um aplicativo para acessar os recursos dos hubs de eventos do Azure
description: Este artigo fornece informações sobre como autenticar um aplicativo com Azure Active Directory para acessar os recursos dos hubs de eventos do Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 818bebdb7555d1b515a5c4a3439d11d4a4766e56
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011977"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticar um aplicativo com Azure Active Directory para acessar recursos de hubs de eventos
O Microsoft Azure fornece gestão de controlo de acesso integrada para os recursos e aplicações com base no Azure Active Directory (Azure AD). Uma vantagem importante de usar o Azure AD com os hubs de eventos do Azure é que você não precisa mais armazenar suas credenciais no código. Em vez disso, você pode solicitar um token de acesso OAuth 2,0 da plataforma de identidade da Microsoft. O Azure AD autentica a entidade de segurança (um usuário, grupo ou entidade de serviço) que executa o aplicativo. Se a autenticação for bem sucedido, o Azure AD retornará um token de acesso para o aplicativo e o aplicativo poderá usar o token de acesso para autorizar a solicitação aos recursos dos hubs de eventos do Azure.

Quando uma função é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível de assinatura, o grupo de recursos, o namespace de hubs de eventos ou qualquer recurso sob ele. Uma segurança do Azure AD pode atribuir funções a um usuário, a um grupo, a uma entidade de serviço de aplicativo ou a uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Uma definição de função é uma coleção de permissões. O RBAC (controle de acesso baseado em função) controla como essas permissões são impostas por meio da atribuição de função. Uma atribuição de função é composta por três elementos: principal de segurança, definição de função e âmbito. Para obter mais informações, consulte [noções básicas sobre as diferentes funções](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Funções internas para hubs de eventos do Azure
O Azure fornece as seguintes funções RBAC internas para autorizar o acesso aos dados dos hubs de eventos usando o Azure AD e o OAuth:

- [Proprietário dos dados dos hubs de eventos do Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Use essa função para fornecer acesso completo aos recursos dos hubs de eventos.
- [Remetente de dados dos hubs de eventos do Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Use essa função para dar acesso de envio aos recursos dos hubs de eventos.
- [Receptor de dados dos hubs de eventos do Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Use essa função para dar acesso ao recebimento de recursos de hubs de eventos.   

> [!IMPORTANT]
> Nossa versão de visualização suportava a adição de privilégios de acesso a dados dos hubs de eventos à função de proprietário ou colaborador. No entanto, os privilégios de acesso a dados para a função de proprietário e colaborador não são mais respeitados. Se você estiver usando a função de proprietário ou colaborador, mude para usando a função de proprietário de dados dos hubs de eventos do Azure.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções de RBAC usando o portal do Azure  
Para saber mais sobre como gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure, consulte [Este artigo](..//role-based-access-control/role-assignments-portal.md). 

Depois de determinar o escopo apropriado para uma atribuição de função, navegue até esse recurso na portal do Azure. Exiba as configurações de controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições de função:

> [!NOTE]
> As etapas descritas abaixo atribuem uma função ao Hub de eventos nos namespaces dos hubs de eventos, mas você pode seguir as mesmas etapas para atribuir uma função com escopo a qualquer recurso de hubs de eventos.

1. Na [portal do Azure](https://portal.azure.com/), navegue até o namespace de seus hubs de eventos.
2. Na página **visão geral** , selecione o Hub de eventos para o qual você deseja atribuir uma função.

    ![Selecione seu hub de eventos](./media/authenticate-application/select-event-hub.png)
1. Selecione **controle de acesso (iam)** para exibir as configurações de controle de acesso para o Hub de eventos. 
1. Selecione a guia atribuições de **função** para ver a lista de atribuições de função. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **Adicionar atribuição de função**. 

    ![Botão Adicionar na barra de ferramentas](./media/authenticate-application/role-assignments-add-button.png)
1. Na página **Adicionar atribuição de função** , execute as seguintes etapas:
    1. Selecione a **função de hubs de eventos** que você deseja atribuir. 
    1. Pesquise para localizar a **entidade de segurança** (usuário, grupo, entidade de serviço) à qual você deseja atribuir a função.
    1. Selecione **salvar** para salvar a atribuição de função. 

        ![Atribuir função a um usuário](./media/authenticate-application/assign-role-to-user.png)
    4. A identidade para a qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que os usuários do Azure estão na função de proprietário de dados dos hubs de eventos do Azure. 
        
        ![Usuário na lista](./media/authenticate-application/user-in-list.png)

Você pode seguir etapas semelhantes para atribuir um escopo de função ao namespace de hubs de eventos, ao grupo de recursos ou à assinatura. Depois de definir a função e seu escopo, você pode testar esse comportamento com exemplos [neste local do GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).


## <a name="authenticate-from-an-application"></a>Autenticar a partir de uma aplicação
Uma vantagem importante de usar o Azure AD com os hubs de eventos é que suas credenciais não precisam mais ser armazenadas em seu código. Em vez disso, você pode solicitar um token de acesso OAuth 2,0 da plataforma de identidade da Microsoft. O Azure AD autentica a entidade de segurança (um usuário, um grupo ou uma entidade de serviço) que executa o aplicativo. Se a autenticação for bem sucedido, o Azure AD retornará o token de acesso para o aplicativo e o aplicativo poderá usar o token de acesso para autorizar solicitações para os hubs de eventos do Azure.

As seções a seguir mostram como configurar seu aplicativo nativo ou aplicativo Web para autenticação com a plataforma de identidade da Microsoft 2,0. Para obter mais informações sobre a plataforma de identidade da Microsoft 2,0, consulte [visão geral da plataforma Microsoft Identity (v 2.0)](../active-directory/develop/v2-overview.md).

Para uma descrição geral do fluxo de concessão de código do OAuth 2.0, consulte [fluxo de concessão de acesso de autorizar a aplicações de web do Azure Active Directory usando o código de OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registar a aplicação com um inquilino do Azure AD
A primeira etapa no uso do Azure AD para autorizar recursos de hubs de eventos é registrar seu aplicativo cliente com um locatário do Azure AD do [portal do Azure](https://portal.azure.com/). Ao registrar seu aplicativo cliente, você fornece informações sobre o aplicativo para o AD. Em seguida, o Azure AD fornece uma ID do cliente (também chamada de ID do aplicativo) que você pode usar para associar seu aplicativo ao tempo de execução do Azure AD. Para saber mais sobre o ID de cliente, veja [aplicativos e objetos de principal de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

As imagens a seguir mostram as etapas para registrar um aplicativo Web:

![Registar uma aplicação](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se você registrar seu aplicativo como um aplicativo nativo, poderá especificar qualquer URI válido para o URI de redirecionamento. Para aplicativos nativos, esse valor não precisa ser uma URL real. Para aplicativos Web, o URI de redirecionamento deve ser um URI válido, pois ele especifica a URL para a qual os tokens são fornecidos.

Depois de registrar seu aplicativo, você verá a ID do **aplicativo (cliente)** em **configurações**:

![ID do aplicativo registrado](./media/authenticate-application/application-id.png)

Para obter mais informações sobre como registar uma aplicação com o Azure AD, consulte [integrar aplicações com o Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).


### <a name="create-a-client-secret"></a>Criar um segredo do cliente   
O aplicativo precisa de um segredo do cliente para provar sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estas etapas.

1. Navegue até o registro do aplicativo no portal do Azure.
1. Selecione a configuração **certificados & segredos** .
1. Em **segredos do cliente**, selecione **novo segredo do cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de expiração desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é exibido apenas uma vez.

    ![Segredo do cliente](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de cliente para aquisição de token  
Depois de registrar seu aplicativo e conceder permissões de ti para enviar/receber dados nos hubs de eventos do Azure, você pode adicionar código ao seu aplicativo para autenticar uma entidade de segurança e adquirir o token 2,0 do OAuth. Para autenticar e adquirir o token, você pode usar uma das [bibliotecas de autenticação da plataforma de identidade da Microsoft](../active-directory/develop/reference-v2-libraries.md) ou outra biblioteca de software livre que dê suporte a OpenID ou Connect 1,0. Seu aplicativo pode, então, usar o token de acesso para autorizar uma solicitação nos hubs de eventos do Azure.

Para obter uma lista de cenários para os quais há suporte para tokens de aquisição, consulte a seção [cenários](https://aka.ms/msal-net-scenarios) do repositório do GITHUB do [MSAL (biblioteca de autenticação da Microsoft) para .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) .


## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre o RBAC, consulte [o que é o RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md)?
- Para saber como atribuir e gerenciar atribuições de função RBAC com Azure PowerShell, CLI do Azure ou a API REST, consulte estes artigos:
    - [Gerenciar RBAC (controle de acesso baseado em função) com Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gerenciar RBAC (controle de acesso baseado em função) com CLI do Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o RBAC (controle de acesso baseado em função) com a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gerenciar RBAC (controle de acesso baseado em função) com modelos de Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Consulte os seguintes artigos relacionados:
- [Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos de hubs de eventos](authenticate-managed-identity.md)
- [Autenticar solicitações para hubs de eventos do Azure usando assinaturas de acesso compartilhado](authenticate-shared-access-signature.md)
- [Autorizar o acesso aos recursos de hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizar o acesso a recursos de hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)

