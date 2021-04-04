---
title: Autenticar uma aplicação para aceder a entidades de autocarros da Azure Service
description: Este artigo fornece informações sobre a autenticação de uma aplicação com o Azure Ative Directory para aceder a entidades de autocarros de serviço da Azure (filas, tópicos, etc.)
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: c4e19c0ab26d491ba0b95159e274383431aefaee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518233"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Autenticar e autorizar uma aplicação com a Azure Ative Directory para aceder a entidades do Azure Service Bus
A Azure Service Bus suporta a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos a entidades de Service Bus (filas, tópicos, subscrições ou filtros). Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (Azure RBAC) para conceder permissões a um principal de segurança, que pode ser um utilizador, grupo ou diretor de serviço de aplicação. Para saber mais sobre papéis e atribuições de papéis, consulte [compreender os diferentes papéis.](../role-based-access-control/overview.md)

## <a name="overview"></a>Descrição Geral
Quando um principal de segurança (um utilizador, grupo ou aplicação) tenta aceder a uma entidade do Service Bus, o pedido deve ser autorizado. Com o Azure AD, o acesso a um recurso é um processo em duas etapas. 

 1. Primeiro, a identidade do diretor de segurança é autenticada, e um token OAuth 2.0 é devolvido. O nome do recurso para solicitar um token é `https://servicebus.azure.net` .
 1. Em seguida, o token é passado como parte de um pedido ao serviço service bus para autorizar o acesso ao recurso especificado.

O passo de autenticação requer que um pedido de candidatura contenha um token de acesso OAuth 2.0 no tempo de execução. Se uma aplicação estiver a ser executada dentro de uma entidade Azure, como um Azure VM, um conjunto de escala de máquina virtual ou uma aplicação Azure Function, pode usar uma identidade gerida para aceder aos recursos. Para aprender a autenticar pedidos feitos por uma identidade gerida para o serviço Service Bus, consulte [autenticar o acesso aos recursos da Azure Service Bus com o Azure Ative Directory e identidades geridas para a Azure Resources.](service-bus-managed-service-identity.md) 

O passo de autorização requer que uma ou mais funções da Azure sejam atribuídas ao diretor de segurança. A Azure Service Bus fornece funções Azure que englobam conjuntos de permissões para recursos de Service Bus. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para saber mais sobre a atribuição de papéis da Azure service bus, consulte [as funções incorporadas da Azure Service Bus.](#azure-built-in-roles-for-azure-service-bus) 

As aplicações nativas e aplicações web que fazem pedidos para Service Bus também podem autorizar com a Azure AD. Este artigo mostra-lhe como solicitar um token de acesso e usá-lo para autorizar pedidos de recursos de Service Bus. 


## <a name="assigning-azure-roles-for-access-rights"></a>Atribuir funções da Azure para direitos de acesso
O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos seguros através do [Azure RBAC](../role-based-access-control/overview.md). A Azure Service Bus define um conjunto de funções incorporadas Azure que englobam conjuntos comuns de permissões usadas para aceder a entidades de Service Bus e também pode definir funções personalizadas para aceder aos dados.

Quando um papel de Azure é atribuído a um diretor de segurança da AZure, a Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser alargado ao nível de subscrição, ao grupo de recursos ou ao espaço de nomes do Service Bus. Um diretor de segurança Azure AD pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma [identidade gerida para os recursos da Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Azure funções incorporadas para Azure Service Bus
Para a Azure Service Bus, a gestão de espaços de nome e todos os recursos relacionados através do portal Azure e da gestão de recursos AZure API já está protegida usando o modelo Azure RBAC. A Azure fornece as funções incorporadas abaixo do Azure para autorizar o acesso a um espaço de nomes de Autocarros de Serviço:

- [Azure Service Bus Data Owner](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Permite o acesso de dados ao espaço de nomes do Service Bus e das suas entidades (filas, tópicos, subscrições e filtros)
- [Azure Service Bus Data Sender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Use esta função para dar acesso ao espaço de nomes do Service Bus e das suas entidades.
- [Azure Service Bus Data Receiver](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Use esta função para dar acesso ao espaço de nomes do Service Bus e das suas entidades. 

## <a name="resource-scope"></a>Âmbito do recurso 
Antes de atribuir um papel de Azure a um diretor de segurança, determine o âmbito de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o âmbito mais estreito possível.

A lista a seguir descreve os níveis em que pode aceder aos recursos do Service Bus, começando pelo âmbito mais restrito:

- **Fila,** **tópico**, ou **subscrição**: A atribuição de funções aplica-se à entidade específica do Service Bus. Atualmente, o portal Azure não suporta a atribuição de utilizadores/grupos/identidades geridas às funções de Service Bus Azure ao nível da subscrição. 
- **Espaço de nome do Service Bus**: A atribuição de funções abrange toda a topologia do Service Bus sob o espaço de nome e para o grupo de consumidores que lhe está associado.
- **Grupo de recursos**: A atribuição de funções aplica-se a todos os recursos do Service Bus no âmbito do grupo de recursos.
- **Subscrição**: A atribuição de funções aplica-se a todos os recursos do Service Bus em todos os grupos de recursos da subscrição.

> [!NOTE]
> Tenha em mente que as atribuições de funções do Azure podem demorar até cinco minutos a propagar-se. 

Para obter mais informações sobre como as funções incorporadas são [definidas, consulte as definições de funções](../role-based-access-control/role-definitions.md#management-and-data-operations)de entender. Para obter informações sobre a criação de funções personalizadas [Azure, consulte os papéis personalizados da Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-azure-roles-using-the-azure-portal"></a>Atribuir funções Azure usando o portal Azure  
Para saber mais sobre a gestão do acesso aos recursos do Azure utilizando o Azure RBAC e o portal Azure, consulte [este artigo](..//role-based-access-control/role-assignments-portal.md). 

Depois de ter determinado a possibilidade adequada para uma atribuição de funções, navegue para esse recurso no portal Azure. Mostrar as definições de controlo de acesso (IAM) para o recurso e seguir estas instruções para gerir as atribuições de funções:

> [!NOTE]
> Os passos descritos abaixo atribuem uma função ao seu espaço de nomes de Service Bus. Pode seguir os mesmos passos para atribuir uma função a outros âmbitos suportados (grupo de recursos, subscrição, etc.).

1. No [portal Azure,](https://portal.azure.com/)navegue para o seu espaço de nomes de Service Bus. Selecione **Access Control (IAM)** no menu esquerdo para exibir as definições de controlo de acesso para o espaço de nomes. Se precisar de criar um espaço de nomes de Service Bus, siga as instruções deste artigo: [Crie um espaço de nome de mensagens de autocarro de serviço](service-bus-create-namespace-portal.md).

    ![Selecione Controlo de Acesso no menu esquerdo](./media/authenticate-application/select-access-control-menu.png)
1. Selecione o **separador funções** para ver a lista de atribuições de funções. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **Adicionar a atribuição de função**. 

    ![Adicione botão na barra de ferramentas](./media/authenticate-application/role-assignments-add-button.png)
1. Na página **de atribuição de funções Adicionar,** faça os seguintes passos:
    1. Selecione a **função de ônibus de serviço** que deseja atribuir. 
    1. Procure localizar o **principal de segurança** (utilizador, grupo, principal serviço) ao qual pretende atribuir a função.
    1. **Selecione Guardar** para guardar a atribuição de funções. 

        ![Atribuir função a um utilizador](./media/authenticate-application/assign-role-to-user.png)
    4. A identidade a quem atribuiu o papel aparece listada nessa função. Por exemplo, a seguinte imagem mostra que os utilizadores do Azure estão na função de Proprietário de Dados de Autocarros do Serviço Azure. 
        
        ![Utilizador na lista](./media/authenticate-application/user-in-list.png)

Pode seguir passos semelhantes para atribuir uma função a um grupo de recursos ou a uma subscrição. Uma vez definido o papel e o seu âmbito, pode testar este comportamento com as [amostras no GitHub.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)


## <a name="authenticate-from-an-application"></a>Autenticar a partir de uma aplicação
Uma vantagem fundamental da utilização do Azure AD com o Service Bus é que as suas credenciais já não precisam de ser armazenadas no seu código. Em vez disso, pode solicitar um token de acesso OAuth 2.0 da plataforma de identidade da Microsoft. A Azure AD autentica o principal de segurança (um utilizador, um grupo ou diretor de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve o token de acesso à aplicação, e a aplicação pode então utilizar o token de acesso para autorizar pedidos à Azure Service Bus.

As secções seguintes mostram-lhe como configurar a sua aplicação nativa ou aplicação web para autenticação com a plataforma de identidade Microsoft 2.0. Para obter mais informações sobre a plataforma de identidade 2.0 da Microsoft, consulte a [plataforma de identidade da Microsoft (v2.0) visão geral](../active-directory/develop/v2-overview.md).

Para obter uma visão geral do fluxo de concessão de código OAuth 2.0, consulte [o acesso autorizado às aplicações web do Azure Ative Directory utilizando o fluxo de concessão de código OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registe o seu pedido junto de um inquilino da AD Azure
O primeiro passo para usar a Azure AD para autorizar entidades de Service Bus é registar a sua candidatura ao cliente com um inquilino AZure AD do [portal Azure.](https://portal.azure.com/) Quando regista a sua candidatura ao cliente, fornece informações sobre o pedido à AD. A Azure AD fornece então um ID do cliente (também chamado de ID de aplicação) que pode usar para associar a sua aplicação ao tempo de execução da Azure AD. Para saber mais sobre o ID do cliente, consulte [os objetos principais de aplicação e serviço no Azure Ative Directory.](../active-directory/develop/app-objects-and-service-principals.md) 

As seguintes imagens mostram passos para registar uma aplicação web:

![Registar uma aplicação](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se registar a sua candidatura como uma aplicação nativa, pode especificar qualquer URI válido para o URI de redirecionamento. Para aplicações nativas, este valor não tem de ser um URL real. Para aplicações web, o URI de redirecionamento deve ser um URI válido, pois especifica o URL para o qual são fornecidos tokens.

Depois de ter registado a sua candidatura, verá o **ID de Aplicação (cliente)** em **Definições:**

![ID de aplicação do pedido registado](./media/authenticate-application/application-id.png)

Para obter mais informações sobre o registo de uma candidatura com a Azure AD, consulte [integrar aplicações com o Azure Ative Directory](../active-directory/develop/quickstart-register-app.md).

> [!IMPORTANT]
> Tome nota do **TenantId** e do **ApplicationId.** Vai precisar destes valores para executar a aplicação.

### <a name="create-a-client-secret"></a>Criar um segredo de cliente   
O pedido precisa de um segredo de cliente para provar a sua identidade ao pedir um token. Para adicionar o segredo do cliente, siga estes passos.

1. Navegue para o registo da sua aplicação no portal Azure se ainda não estiver na página.
1. Selecione **Certificados & segredos** no menu esquerdo.
1. Sob **os segredos do Cliente,** selecione **Novo segredo de cliente** para criar um novo segredo.

    ![Novo segredo do cliente - botão](./media/authenticate-application/new-client-secret-button.png)
1. Forneça uma descrição para o segredo e escolha o intervalo de validade desejado e, em seguida, selecione **Add**.

    ![Adicionar página secreta do cliente](./media/authenticate-application/add-client-secret-page.png)
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é apresentado apenas uma vez.

    ![Segredo do cliente](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Permissões para a API de autocarro de serviço
Se a sua aplicação for uma aplicação de consola, deve registar uma aplicação nativa e adicionar permissões API para **o Microsoft.ServiceBus** às **permissões necessárias definidas.** As aplicações nativas também precisam de um **redirect-URI** em Azure AD, que serve como identificador; o URI não precisa de ser um destino de rede. Use `https://servicebus.microsoft.com` para este exemplo, porque o código de amostra já utiliza o URI.

### <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de clientes para aquisição simbólica  
Uma vez registado o seu pedido e concedido permissões para enviar/receber dados no Azure Service Bus, pode adicionar código à sua aplicação para autenticar um titular de segurança e adquirir o token OAuth 2.0. Para autenticar e adquirir o token, pode utilizar uma das bibliotecas de [autenticação](../active-directory/develop/reference-v2-libraries.md) da plataforma de identidade da Microsoft ou outra biblioteca de código aberto que suporte o OpenID ou o Connect 1.0. A sua aplicação pode então utilizar o token de acesso para autorizar um pedido contra a Azure Service Bus.

Para obter uma lista de cenários para os quais a aquisição de fichas é suportada, consulte a secção [de Cenários](https://aka.ms/msal-net-scenarios) da Biblioteca de Autenticação da [Microsoft (MSAL) para](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) o repositório .NET GitHub.

## <a name="sample-on-github"></a>Amostra no GitHub
Consulte a seguinte amostra no GitHub: [Controlo de acesso baseado em funções Azure para Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Utilize a opção De Login Secreto do **Cliente,** não a opção **De Início de Sessão do Utilizador Interativo.** Quando se usa a opção secreta do cliente, não se vê uma janela pop-up. A aplicação utiliza o ID do inquilino e o ID da aplicação para autenticação. 

### <a name="run-the-sample"></a>Executar o exemplo

Antes de poder executar a amostra, edite o ficheiro **app.config** e, dependendo do seu cenário, dediija os seguintes valores:

- `tenantId`: Definir para o valor **de TenantId.**
- `clientId`: Definir para o valor **ApplicationId.**
- `clientSecret`: Se quiser iniciar sessão utilizando o segredo do cliente, crie-o em Azure AD. Além disso, use uma aplicação web ou API em vez de uma aplicação nativa. Além disso, adicione a aplicação no **Controlo de Acesso (IAM)** no espaço de nome que criou anteriormente.
- `serviceBusNamespaceFQDN`: Desa fixação ao nome COMPLETO DNS do seu espaço de nomes do Service Bus recém-criado; por exemplo, `example.servicebus.windows.net` . .
- `queueName`: Desa estalem o nome da fila que criaram.
- O URI de redirecionamento especificado na sua aplicação nos passos anteriores.

Quando executar a aplicação da consola, é-lhe solicitado que selecione um cenário. Selecione **o Início de Sessão do Utilizador Interativo** digitando o seu número e pressionando ENTER. A aplicação apresenta uma janela de login, pede o seu consentimento para aceder ao Service Bus e, em seguida, utiliza o serviço para executar através do cenário de envio/receção utilizando a identidade de login.


## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre o Azure RBAC, veja [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)](../role-based-access-control/overview.md)?
- Para aprender a atribuir e gerir atribuições de funções da Azure PowerShell, Azure CLI ou da REST API, consulte estes artigos:
    - [Utilizar o Azure PowerShell para adicionar ou remover atribuições de funções do Azure](../role-based-access-control/role-assignments-powershell.md)  
    - [Utilizar a CLI do Azure para adicionar ou remover atribuições de funções do Azure](../role-based-access-control/role-assignments-cli.md)
    - [Utilizar a API REST para adicionar ou remover atribuições de funções do Azure](../role-based-access-control/role-assignments-rest.md)
    - [Adicione ou remova atribuições de funções Azure usando modelos de gestor de recursos Azure](../role-based-access-control/role-assignments-template.md)

Para mais informações sobre mensagens do Service Bus, consulte os seguintes tópicos.

- [Amostras de Azure RBAC de ônibus de serviço](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
- [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)