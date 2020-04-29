---
title: Autenticar uma aplicação de acesso a entidades azure service bus
description: Este artigo fornece informações sobre a autenticação de uma aplicação com o Azure Ative Directory para aceder às entidades azure service bus (filas, tópicos, etc.)
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259295"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Autenticar e autorizar uma candidatura com o Diretório Ativo azure para aceder às entidades da Azure Service Bus
O Azure Service Bus suporta a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos a entidades de Ônibus de serviço (filas, tópicos, subscrições ou filtros). Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões a um diretor de segurança, que pode ser um utilizador, grupo ou diretor de serviço de aplicação. Para saber mais sobre papéis e atribuições de papéis, consulte [compreender os diferentes papéis.](../role-based-access-control/overview.md)

## <a name="overview"></a>Descrição geral
Quando um diretor de segurança (utilizador, grupo ou aplicação) tenta aceder a uma entidade do Ônibus de serviço, o pedido deve ser autorizado. Com a AD Azure, o acesso a um recurso é um processo em duas etapas. 

 1. Primeiro, a identidade do diretor de segurança é autenticada, e um token OAuth 2.0 é devolvido. O nome do recurso para `https://servicebus.azure.net`pedir um símbolo é .
 1. Em seguida, o símbolo é passado como parte de um pedido ao serviço de ônibus de serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um pedido de pedido contenha um sinal de acesso OAuth 2.0 no prazo de execução. Se uma aplicação estiver a funcionar dentro de uma entidade Azure, como um Azure VM, um conjunto de escala de máquina virtual ou uma aplicação Azure Function, pode usar uma identidade gerida para aceder aos recursos. Para saber autenticar pedidos feitos por uma identidade gerida para o serviço de ônibus de serviço, consulte o [acesso authenticado aos recursos do Azure Service Bus com o Azure Ative Directory e identidades geridas para a Azure Resources.](service-bus-managed-service-identity.md) 

O passo de autorização requer que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. O Azure Service Bus fornece funções RBAC que englobam conjuntos de permissões para recursos de ônibus de serviço. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para saber mais sobre a atribuição de funções RBAC ao Azure Service Bus, consulte [as funções RBAC incorporadas para o Azure Service Bus.](#built-in-rbac-roles-for-azure-service-bus) 

Aplicações nativas e aplicações web que fazem pedidos para O Ônibus de Serviço também podem autorizar com a Azure AD. Este artigo mostra-lhe como solicitar um sinal de acesso e usá-lo para autorizar pedidos de recursos de ônibus de serviço. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Atribuição de funções RBAC para direitos de acesso
O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos garantidos através do [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md). O Azure Service Bus define um conjunto de funções RBAC incorporadas que englobam conjuntos comuns de permissões usadas para aceder a entidades de ônibus de serviço e também pode definir funções personalizadas para aceder aos dados.

Quando uma função RBAC é atribuída a um diretor de segurança da AD Azure, o Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser consultado ao nível de subscrição, ao grupo de recursos ou ao espaço de nome do Bus de Serviço. Um diretor de segurança da AD Azure pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma [identidade gerida para os recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Funções RBAC incorporadas para ônibus de serviço Azure
Para o Azure Service Bus, a gestão de espaços de nome e todos os recursos conexos através do portal Azure e da API de gestão de recursos Azure já está protegida utilizando o modelo de controlo de acesso baseado *em funções* (RBAC). O Azure fornece as funções RBAC incorporadas abaixo para autorizar o acesso a um espaço de nome de autocarro de serviço:

- [Azure Service Bus Data Owner](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Permite o acesso de dados ao espaço de nome do Bus de Serviço e às suas entidades (filas, tópicos, subscrições e filtros)
- Remetente de dados do [ônibus azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)service : Use esta função para dar acesso ao espaço de nome do Bus de Serviço e suas entidades.
- Recetor de dados de [ônibus de serviço Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Use esta função para dar acesso ao espaço de nome do Bus de Serviço e suas entidades. 

## <a name="resource-scope"></a>Âmbito de recurso 
Antes de atribuir uma função RBAC a um diretor de segurança, determine o alcance de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o âmbito mais estreito possível.

A lista seguinte descreve os níveis a que pode aceder aos recursos do Ônibus de serviço, começando pelo âmbito mais restrito:

- **Fila,** **tópico,** ou **subscrição**: Atribuição de funções aplica-se à entidade específica do Ônibus de Serviço. Atualmente, o portal Azure não suporta atribuir utilizadores/grupos/identidades geridas às funções De Serviço de RBAC ao nível da subscrição. 
- **Service Bus namespace**: A atribuição de funções abrange toda a topologia do Service Bus sob o espaço de nome e para o grupo de consumidores associado ao mesmo.
- **Grupo de recursos**: A atribuição de funções aplica-se a todos os recursos do Service Bus no âmbito do grupo de recursos.
- **Subscrição**: A atribuição de funções aplica-se a todos os recursos do Service Bus em todos os grupos de recursos na subscrição.

> [!NOTE]
> Tenha em mente que as atribuições de funções RBAC podem demorar até cinco minutos para se propagar. 

Para obter mais informações sobre como os papéis incorporados são definidos, consulte [compreender definições](../role-based-access-control/role-definitions.md#management-and-data-operations)de papéis . Para obter informações sobre a criação de funções RBAC personalizadas, consulte [Criar papéis personalizados para o Controlo de Acesso baseado em papel Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções RBAC utilizando o portal Azure  
Para saber mais sobre a gestão do acesso aos recursos Do Azure utilizando o RBAC e o portal Azure, consulte [este artigo.](..//role-based-access-control/role-assignments-portal.md) 

Depois de ter determinado a margem adequada para uma atribuição de funções, navegue para esse recurso no portal Azure. Mostrar as definições de controlo de acesso (IAM) para o recurso e seguir estas instruções para gerir as atribuições de funções:

> [!NOTE]
> Os passos abaixo descritos atribui uma função ao seu espaço de nome do Ônibus de serviço. Pode seguir os mesmos passos para atribuir uma função a outros âmbitos suportados (grupo de recursos, subscrição, etc.).

1. No [portal Azure,](https://portal.azure.com/)navegue para o seu espaço de nome service Bus. Selecione **Control de Acesso (IAM)** no menu esquerdo para visualizar as definições de controlo de acesso para o espaço de nome. Se precisar de criar um espaço de nome service Bus, siga as instruções deste artigo: Criar um espaço de nome de mensagens de [ônibus de serviço](service-bus-create-namespace-portal.md).

    ![Selecione Controlo de Acesso no menu esquerdo](./media/authenticate-application/select-access-control-menu.png)
1. Selecione o separador de **atribuições de funções** para ver a lista de atribuições de papéis. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **adicionar a atribuição de funções**. 

    ![Adicione o botão na barra de ferramentas](./media/authenticate-application/role-assignments-add-button.png)
1. Na página de atribuição de **funções Adicionar,** faça os seguintes passos:
    1. Selecione a **função De Ônibus de Serviço** que pretende atribuir. 
    1. Procure localizar o diretor de **segurança** (utilizador, grupo, diretor de serviço) ao qual pretende atribuir a função.
    1. Selecione **Guardar** para salvar a atribuição de funções. 

        ![Atribuir papel a um utilizador](./media/authenticate-application/assign-role-to-user.png)
    4. A identidade a quem atribuiu o papel aparece listada nesse papel. Por exemplo, a imagem que se segue mostra que os utilizadores do Azure estão na função de Proprietário de Dados de Ônibus de Serviço Azure. 
        
        ![Utilizador na lista](./media/authenticate-application/user-in-list.png)

Pode seguir passos semelhantes para atribuir uma função ao seu alcance ou subscrição. Uma vez definido o papel e o seu âmbito, pode testar este comportamento com as [amostras no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Autenticar a partir de uma aplicação
Uma vantagem fundamental de usar o Azure AD com o Service Bus é que as suas credenciais já não precisam de ser armazenadas no seu código. Em vez disso, pode solicitar um sinal de acesso OAuth 2.0 da plataforma de identidade da Microsoft. A Azure AD autentica o diretor de segurança (um utilizador, um grupo ou diretor de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve o sinal de acesso à aplicação, podendo então utilizar o sinal de acesso para autorizar pedidos ao Azure Service Bus.

As seguintes secções mostram-lhe como configurar a sua aplicação nativa ou aplicação web para autenticação com a plataforma de identidade Microsoft 2.0. Para obter mais informações sobre a plataforma de identidade da Microsoft 2.0, consulte a visão geral da [plataforma de identidade da Microsoft (v2.0).](../active-directory/develop/v2-overview.md)

Para uma visão geral do fluxo de concessão de código OAuth 2.0, consulte [Autorizar o acesso às aplicações web do Azure Ative Directory utilizando o fluxo de subvenção de código OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registe a sua candidatura com um inquilino da AD Azure
O primeiro passo na utilização da Azure AD para autorizar entidades de ônibus de serviço é registar a sua aplicação de cliente com um inquilino Azure AD do [portal Azure](https://portal.azure.com/). Ao registar a sua aplicação de cliente, fornece informações sobre a aplicação à AD. A Azure AD fornece então um ID do cliente (também chamado de ID de aplicação) que pode utilizar para associar a sua aplicação ao tempo de execução da AD Azure. Para saber mais sobre o ID do cliente, consulte [os objetos principais de aplicação e serviço no Diretório Ativo Azure](../active-directory/develop/app-objects-and-service-principals.md). 

As seguintes imagens mostram passos para registar uma aplicação web:

![Registar uma aplicação](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se registar a sua candidatura como uma aplicação nativa, pode especificar qualquer URI válido para o Redirect URI. Para aplicações nativas, este valor não tem de ser um URL real. Para aplicações web, o URI redirecionado deve ser um URI válido, porque especifica o URL a que são fornecidos tokens.

Depois de ter registado o seu pedido, verá o ID de **Aplicação (cliente)** em **Definições:**

![Identificação de pedido do pedido registado](./media/authenticate-application/application-id.png)

Para obter mais informações sobre o registo de uma aplicação com a Azure AD, consulte a Integração de [aplicações com o Diretório Ativo Azure](../active-directory/develop/quickstart-v2-register-an-app.md).

> [!IMPORTANT]
> Tome nota do **TenantId** e do **ApplicationId**. Você precisará destes valores para executar a aplicação.

### <a name="create-a-client-secret"></a>Criar um segredo de cliente   
O pedido precisa de um segredo de cliente para provar a sua identidade ao solicitar um símbolo. Para adicionar o segredo do cliente, siga estes passos.

1. Navegue para o registo da sua aplicação no portal Azure se ainda não estiver na página.
1. Selecione **Certificados & segredos** no menu esquerdo.
1. Sob **os segredos do Cliente,** selecione **novo segredo de cliente** para criar um novo segredo.

    ![Novo segredo do cliente - botão](./media/authenticate-application/new-client-secret-button.png)
1. Forneça uma descrição para o segredo e escolha o intervalo de validade desejado e, em seguida, selecione **Adicionar**.

    ![Adicionar página secreta do cliente](./media/authenticate-application/add-client-secret-page.png)
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é-lhe apresentado apenas uma vez.

    ![Segredo do cliente](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Permissões para a API de ônibus de serviço
Se a sua aplicação for uma aplicação de consola, deve registar uma aplicação nativa e adicionar permissões API para **microsoft.ServiceBus** ao conjunto de **permissões necessários.** As aplicações nativas também precisam de um **redirecionamento-URI** em Azure AD, que serve como identificador; o URI não precisa de ser um destino de rede. Utilize `https://servicebus.microsoft.com` para este exemplo, porque o código da amostra já utiliza esse URI.

### <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de clientes para aquisição simbólica  
Depois de ter registado o seu pedido e lhe ter concedido permissões para enviar/receber dados no Azure Service Bus, pode adicionar código à sua aplicação para autenticar um diretor de segurança e adquirir o Token OAuth 2.0. Para autenticar e adquirir o símbolo, pode utilizar uma das bibliotecas de [autenticação](../active-directory/develop/reference-v2-libraries.md) da plataforma de identidade microsoft ou outra biblioteca de código aberto que suporta o OpenID ou o Connect 1.0. A sua aplicação pode então utilizar o sinal de acesso para autorizar um pedido contra o Azure Service Bus.

Para obter uma lista de cenários para os quais é suportado o suporte para a aquisição de fichas, consulte a secção [Cenários](https://aka.ms/msal-net-scenarios) da Biblioteca de Autenticação da [Microsoft (MSAL) para o](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) repositório .NET GitHub.

## <a name="sample-on-github"></a>Amostra no GitHub
Consulte a seguinte amostra no GitHub: [Controlo de acesso de base de funções para autocarro](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)de serviço . 

Utilize a opção **de login do Segredo do Cliente,** e não a opção **de login do utilizador interativo.** Quando usas a opção secreta do cliente, não vês uma janela pop-up. A aplicação utiliza o ID do inquilino e o ID da aplicação para autenticação. 

### <a name="run-the-sample"></a>Executar o exemplo

Antes de poder executar a amostra, edite o ficheiro **app.config** e, dependendo do seu cenário, detete os seguintes valores:

- `tenantId`: Definido para o valor **Do Id inquilino.**
- `clientId`: Definir para o valor **ApplicationId.**
- `clientSecret`: Se quiser assinar com o segredo do cliente, crie-o em Azure AD. Além disso, utilize uma aplicação web ou API em vez de uma aplicação nativa. Além disso, adicione a aplicação no **Access Control (IAM)** no espaço de nome que criou anteriormente.
- `serviceBusNamespaceFQDN`: Definir o nome DNS completo do seu recém-criado espaço de nome service bus; por exemplo, `example.servicebus.windows.net`.
- `queueName`: Definir o nome da fila que criou.
- O URI redirecionado que especificou na sua aplicação nos passos anteriores.

Quando executa a aplicação da consola, é-lhe solicitado que selecione um cenário. Selecione **Login de utilizador interativo** digitando o seu número e premindo ENTER. A aplicação exibe uma janela de login, pede o seu consentimento para aceder ao Service Bus e, em seguida, utiliza o serviço para executar através do cenário de envio/receção usando a identidade de login.


## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre o RBAC, veja [O que é o controlo de acesso baseado em papéis (RBAC)](../role-based-access-control/overview.md)?
- Para aprender a atribuir e gerir atribuições de funções RBAC com a Azure PowerShell, Azure CLI ou a REST API, consulte estes artigos:
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gerir o controlo de acesso baseado em funções (RBAC) com o Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com modelos de gestor de recursos Azure](../role-based-access-control/role-assignments-template.md)

Para mais informações sobre mensagens do Service Bus, consulte os seguintes tópicos.

- [Amostras rBAC de ônibus de serviço](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
- [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
- [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
