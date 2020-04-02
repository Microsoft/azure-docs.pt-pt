---
title: Autenticar uma aplicação para aceder aos recursos do Azure Event Hubs
description: Este artigo fornece informações sobre a autenticação de uma aplicação com o Azure Ative Directory para aceder aos recursos do Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 4cef49f138b96848b8e59cb5b2d0b185d4568aa9
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521004"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticar uma aplicação com o Diretório Ativo Azure para aceder aos recursos do Event Hubs
O Microsoft Azure fornece uma gestão integrada do controlo de acesso para recursos e aplicações com base no Azure Ative Directory (Azure AD). Uma vantagem fundamental de usar o Azure AD com o Azure Event Hubs é que já não precisa de armazenar as suas credenciais no código. Em vez disso, pode solicitar um sinal de acesso OAuth 2.0 da plataforma Microsoft Identity. O nome do recurso para `https://eventhubs.azure.net/` solicitar um símbolo é (Para os `https://<namespace>.servicebus.windows.net`clientes Kafka, o recurso para solicitar um símbolo é). A Azure AD autentica o diretor de segurança (utilizador, grupo ou diretor de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve um sinal de acesso à aplicação, podendo então utilizar o sinal de acesso para autorizar o pedido aos recursos do Azure Event Hubs.

Quando uma função é atribuída a um diretor de segurança da AD Azure, o Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser consultado ao nível de subscrição, ao grupo de recursos, ao espaço de nome sem nome do Event Hubs ou a qualquer recurso que lhe seja imputado. Uma segurança azure AD pode atribuir funções a um utilizador, a um grupo, a um diretor de serviço de aplicação ou a uma [identidade gerida para os recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md) 

> [!NOTE]
> Uma definição de função é uma coleção de permissões. O controlo de acesso baseado em funções (RBAC) controla a forma como estas permissões são aplicadas através da atribuição de funções. Uma atribuição de função é composta por três elementos: principal de segurança, definição de função e âmbito. Para mais informações, consulte [Compreender os diferentes papéis.](../role-based-access-control/overview.md)

## <a name="built-in-roles-for-azure-event-hubs"></a>Papéis integrados para hubs de eventos azure
A Azure fornece as seguintes funções de RBAC incorporadopara autorizar o acesso aos dados do Event Hubs utilizando a Azure AD e a OAuth:

- [Azure Event Hubs Data Owner](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Use esta função para dar acesso completo aos recursos do Event Hubs.
- [Remetente de dados do Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Use esta função para dar acesso aos recursos do Event Hubs.
- Recetor de [dados do Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Utilize esta função para dar acesso aos recursos do Event Hubs.   

> [!IMPORTANT]
> A nossa versão de pré-visualização apoiou a adição de privilégios de acesso a dados do Event Hubs ao papel de Proprietário ou Colaborador. No entanto, os privilégios de acesso a dados para o papel de Proprietário e Colaborador já não são honrados. Se estiver a utilizar a função Proprietário ou Colaborador, mude para utilizar a função de Proprietário de Dados do Azure Event Hubs.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções RBAC utilizando o portal Azure  
Para saber mais sobre a gestão do acesso aos recursos Do Azure utilizando o RBAC e o portal Azure, consulte [este artigo.](..//role-based-access-control/role-assignments-portal.md) 

Depois de ter determinado a margem adequada para uma atribuição de funções, navegue para esse recurso no portal Azure. Mostrar as definições de controlo de acesso (IAM) para o recurso e seguir estas instruções para gerir as atribuições de funções:

> [!NOTE]
> Os passos abaixo descritos atribui uma função ao seu centro de eventos sob os espaços de nome do Event Hubs, mas você pode seguir os mesmos passos para atribuir uma função amplamente ao recurso do Event Hubs.

1. No [portal Azure,](https://portal.azure.com/)navegue para o seu espaço de nome Sem Nome Do Seu Evento Hubs.
2. Na página **'Visão Geral',** selecione o centro do evento para o qual pretende atribuir uma função.

    ![Selecione o seu centro de eventos](./media/authenticate-application/select-event-hub.png)
1. Selecione **Control de Acesso (IAM)** para visualizar as definições de controlo de acesso para o centro de eventos. 
1. Selecione o separador de **atribuições de funções** para ver a lista de atribuições de papéis. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **adicionar a atribuição de funções**. 

    ![Adicione o botão na barra de ferramentas](./media/authenticate-application/role-assignments-add-button.png)
1. Na página de atribuição de **funções Adicionar,** faça os seguintes passos:
    1. Selecione a **função De Centro de Eventos** que pretende atribuir. 
    1. Procure localizar o diretor de **segurança** (utilizador, grupo, diretor de serviço) ao qual pretende atribuir a função.
    1. Selecione **Guardar** para salvar a atribuição de funções. 

        ![Atribuir papel a um utilizador](./media/authenticate-application/assign-role-to-user.png)
    4. A identidade a quem atribuiu o papel aparece listada nesse papel. Por exemplo, a imagem que se segue mostra que os utilizadores do Azure estão na função de Proprietário de Dados do Azure Event Hubs. 
        
        ![Utilizador na lista](./media/authenticate-application/user-in-list.png)

Pode seguir passos semelhantes para atribuir uma função ao espaço de nome, grupo de recursos ou subscrição do Event Hubs. Uma vez definido o papel e o seu âmbito, pode testar este comportamento com amostras [neste local GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).


## <a name="authenticate-from-an-application"></a>Autenticar a partir de uma aplicação
Uma vantagem fundamental de usar o Azure AD com Hubs de Eventos é que as suas credenciais já não precisam de ser armazenadas no seu código. Em vez disso, pode solicitar um sinal de acesso OAuth 2.0 da plataforma de identidade da Microsoft. A Azure AD autentica o diretor de segurança (um utilizador, um grupo ou diretor de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve o sinal de acesso à aplicação, podendo então utilizar o sinal de acesso para autorizar pedidos aos Hubs de Eventos Azure.

As seguintes secções mostram-lhe como configurar a sua aplicação nativa ou aplicação web para autenticação com a plataforma de identidade Microsoft 2.0. Para obter mais informações sobre a plataforma de identidade da Microsoft 2.0, consulte a visão geral da [plataforma de identidade da Microsoft (v2.0).](../active-directory/develop/v2-overview.md)

Para uma visão geral do fluxo de concessão de código OAuth 2.0, consulte [Autorizar o acesso às aplicações web do Azure Ative Directory utilizando o fluxo de subvenção de código OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registe a sua candidatura com um inquilino da AD Azure
O primeiro passo na utilização da Azure AD para autorizar os recursos do Event Hubs é registar a sua aplicação de cliente com um inquilino Azure AD do [portal Azure.](https://portal.azure.com/) Ao registar a sua aplicação de cliente, fornece informações sobre a aplicação à AD. A Azure AD fornece então um ID do cliente (também chamado de ID de aplicação) que pode utilizar para associar a sua aplicação ao tempo de execução da AD Azure. Para saber mais sobre o ID do cliente, consulte [os objetos principais de aplicação e serviço no Diretório Ativo Azure](../active-directory/develop/app-objects-and-service-principals.md). 

As seguintes imagens mostram passos para registar uma aplicação web:

![Registar uma aplicação](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se registar a sua candidatura como uma aplicação nativa, pode especificar qualquer URI válido para o Redirect URI. Para aplicações nativas, este valor não tem de ser um URL real. Para aplicações web, o URI redirecionado deve ser um URI válido, porque especifica o URL a que são fornecidos tokens.

Depois de ter registado o seu pedido, verá o ID de **Aplicação (cliente)** em **Definições:**

![Identificação de pedido do pedido registado](./media/authenticate-application/application-id.png)

Para obter mais informações sobre o registo de uma aplicação com a Azure AD, consulte a Integração de [aplicações com o Diretório Ativo Azure](../active-directory/develop/quickstart-v2-register-an-app.md).


### <a name="create-a-client-secret"></a>Criar um segredo de cliente   
O pedido precisa de um segredo de cliente para provar a sua identidade ao solicitar um símbolo. Para adicionar o segredo do cliente, siga estes passos.

1. Navegue para o registo da sua aplicação no portal Azure.
1. Selecione a definição **de segredos & Certificados.**
1. Sob **os segredos do Cliente,** selecione **novo segredo de cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de validade desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é-lhe apresentado apenas uma vez.

    ![Segredo do cliente](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de clientes para aquisição simbólica  
Uma vez registado o seu pedido e concedido permissão para enviar/receber dados em Hubs de Eventos Azure, pode adicionar código à sua aplicação para autenticar um diretor de segurança e adquirir o Token OAuth 2.0. Para autenticar e adquirir o símbolo, pode utilizar uma das bibliotecas de [autenticação](../active-directory/develop/reference-v2-libraries.md) da plataforma de identidade microsoft ou outra biblioteca de código aberto que suporta o OpenID ou o Connect 1.0. A sua aplicação pode então utilizar o sinal de acesso para autorizar um pedido contra o Azure Event Hubs.

Para obter uma lista de cenários para os quais é suportado o suporte para a aquisição de fichas, consulte a secção [Cenários](https://aka.ms/msal-net-scenarios) da Biblioteca de Autenticação da [Microsoft (MSAL) para o](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) repositório .NET GitHub.

## <a name="samples"></a>Amostras
- [Microsoft.Azure.EventHubs amostras](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Estas amostras utilizam a antiga biblioteca **Microsoft.Azure.EventHubs,** mas pode facilmente atualizá-la para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.** Para mover a amostra da utilização da antiga biblioteca para uma nova, consulte o [Guia para migrar de Microsoft.Azure.EventHubs para Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Amostras de Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Esta amostra foi atualizada para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.**

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre o RBAC, veja [O que é o controlo de acesso baseado em papéis (RBAC)](../role-based-access-control/overview.md)?
- Para aprender a atribuir e gerir atribuições de funções RBAC com a Azure PowerShell, Azure CLI ou a REST API, consulte estes artigos:
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gerir o controlo de acesso baseado em funções (RBAC) com o Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com modelos de gestor de recursos Azure](../role-based-access-control/role-assignments-template.md)

Consulte os seguintes artigos relacionados:
- [Autenticar uma identidade gerida com o Diretório Ativo Azure para aceder aos Recursos hubs do Evento](authenticate-managed-identity.md)
- [Autenticar pedidos aos Hubs de Eventos Azure usando assinaturas de acesso partilhado](authenticate-shared-access-signature.md)
- [Autorizar acesso aos recursos do Event Hubs utilizando o Diretório Ativo Azure](authorize-access-azure-active-directory.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)

