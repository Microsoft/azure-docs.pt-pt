---
title: Autenticar uma aplicação para aceder aos recursos do Azure Event Hubs
description: Este artigo fornece informações sobre a autenticação de uma aplicação com o Azure Ative Directory para aceder aos recursos do Azure Event Hubs
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 50c697e5c430b72f8d5da393e90f1db7ff6d48a1
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332489"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticar uma aplicação com o Azure Ative Directory para aceder aos recursos do Event Hubs
O Microsoft Azure fornece uma gestão integrada do controlo de acessos para recursos e aplicações com base no Azure Ative Directory (Azure AD). Uma vantagem fundamental de usar a Azure AD com Azure Event Hubs é que já não precisa de armazenar as suas credenciais no código. Em vez disso, pode solicitar um token de acesso OAuth 2.0 a partir da plataforma Microsoft Identity. O nome de recurso para solicitar um token é `https://eventhubs.azure.net/` (Para os clientes Kafka, o recurso para solicitar um token é `https://<namespace>.servicebus.windows.net` ). A Azure AD autentica o principal de segurança (um utilizador, grupo ou principal de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve um sinal de acesso à aplicação, e a aplicação pode então utilizar o token de acesso para autorizar o pedido aos recursos do Azure Event Hubs.

Quando um papel é atribuído a um diretor de segurança da AZure, a Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser analisado ao nível de subscrição, ao grupo de recursos, ao espaço de nomes do Event Hubs ou a qualquer recurso sob o mesmo. Uma segurança Azure AD pode atribuir funções a um utilizador, a um grupo, a um diretor de serviço de aplicações ou a uma [identidade gerida para os recursos da Azure.](../active-directory/managed-identities-azure-resources/overview.md) 

> [!NOTE]
> Uma definição de função é uma coleção de permissões. O controlo de acesso baseado em funções (Azure RBAC) controla a forma como estas permissões são aplicadas através da atribuição de funções. Uma atribuição de função é composta por três elementos: principal de segurança, definição de função e âmbito. Para obter mais informações, consulte [compreender as diferentes funções.](../role-based-access-control/overview.md)

## <a name="built-in-roles-for-azure-event-hubs"></a>Papéis incorporados para hubs de eventos Azure
A Azure fornece as seguintes funções incorporadas do Azure para autorizar o acesso aos dados do Event Hubs utilizando Azure AD e OAuth:

- [Azure Event Hubs Data Owner](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Use esta função para dar acesso total aos recursos do Event Hubs.
- [Azure Event Hubs Data Sender](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Use esta função para dar acesso ao envio aos recursos do Event Hubs.
- [Azure Event Hubs Data Receiver](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Use esta função para dar acesso aos recursos do Event Hubs.   

Para funções incorporadas no Registo de Schema, consulte [as funções de Registo de Schema](schema-registry-overview.md#azure-role-based-access-control).

> [!IMPORTANT]
> A nossa versão de pré-visualização suportava a adição de privilégios de acesso de dados do Event Hubs à função Proprietário ou Colaborador. No entanto, os privilégios de acesso a dados para o papel de Proprietário e Colaborador deixaram de ser honrados. Se estiver a utilizar a função Proprietário ou Contribuinte, mude para a função Azure Event Hubs Data Owner.

## <a name="assign-azure-roles-using-the-azure-portal"></a>Atribuir funções Azure usando o portal Azure  
Para saber mais sobre a gestão do acesso aos recursos do Azure utilizando o Azure RBAC e o portal Azure, consulte [este artigo](..//role-based-access-control/role-assignments-portal.md). 

Depois de ter determinado a possibilidade adequada para uma atribuição de funções, navegue para esse recurso no portal Azure. Mostrar as definições de controlo de acesso (IAM) para o recurso e seguir estas instruções para gerir as atribuições de funções:

> [!NOTE]
> Os passos descritos abaixo atribuem uma função ao seu centro de eventos sob os espaços de nomes do Event Hubs, mas pode seguir os mesmos passos para atribuir uma função a qualquer recurso de Event Hubs.

1. No [portal Azure,](https://portal.azure.com/)navegue para o seu espaço de nomes Desempaco.
2. Na página **'Vista Geral',** selecione o centro de eventos para o qual pretende atribuir uma função.

    ![Selecione o seu centro de eventos](./media/authenticate-application/select-event-hub.png)
1. Selecione **Access Control (IAM)** para exibir as definições de controlo de acesso para o centro de eventos. 
1. Selecione o **separador funções** para ver a lista de atribuições de funções. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **Adicionar a atribuição de função**. 

    ![Adicione botão na barra de ferramentas](./media/authenticate-application/role-assignments-add-button.png)
1. Na página **de atribuição de funções Adicionar,** faça os seguintes passos:
    1. Selecione a **função De Centros de Eventos** que pretende atribuir. 
    1. Procure localizar o **principal de segurança** (utilizador, grupo, principal serviço) ao qual pretende atribuir a função.
    1. **Selecione Guardar** para guardar a atribuição de funções. 

        ![Atribuir função a um utilizador](./media/authenticate-application/assign-role-to-user.png)
    4. A identidade a quem atribuiu o papel aparece listada nessa função. Por exemplo, a seguinte imagem mostra que os utilizadores do Azure estão na função de Proprietário de Dados do Azure Event Hubs. 
        
        ![Utilizador na lista](./media/authenticate-application/user-in-list.png)

Pode seguir passos semelhantes para atribuir uma função a scopeed para o espaço de nomes do Event Hubs, grupo de recursos ou subscrição. Uma vez definido o papel e o seu âmbito, pode testar este comportamento com amostras [neste local do GitHub.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)


## <a name="authenticate-from-an-application"></a>Autenticar a partir de uma aplicação
Uma vantagem fundamental da utilização do AZure AD com os Centros de Eventos é que as suas credenciais já não precisam de ser armazenadas no seu código. Em vez disso, pode solicitar um token de acesso OAuth 2.0 da plataforma de identidade da Microsoft. A Azure AD autentica o principal de segurança (um utilizador, um grupo ou diretor de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve o token de acesso à aplicação, e a aplicação pode então utilizar o token de acesso para autorizar pedidos aos Azure Event Hubs.

As secções seguintes mostram-lhe como configurar a sua aplicação nativa ou aplicação web para autenticação com a plataforma de identidade Microsoft 2.0. Para obter mais informações sobre a plataforma de identidade 2.0 da Microsoft, consulte a [plataforma de identidade da Microsoft (v2.0) visão geral](../active-directory/develop/v2-overview.md).

Para obter uma visão geral do fluxo de concessão de código OAuth 2.0, consulte [o acesso autorizado às aplicações web do Azure Ative Directory utilizando o fluxo de concessão de código OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registe o seu pedido junto de um inquilino da AD Azure
O primeiro passo para usar a Azure AD para autorizar os recursos do Event Hubs é registar a sua aplicação de cliente com um inquilino AD Azure do [portal Azure.](https://portal.azure.com/) Quando regista a sua candidatura ao cliente, fornece informações sobre o pedido à AD. A Azure AD fornece então um ID do cliente (também chamado de ID de aplicação) que pode usar para associar a sua aplicação ao tempo de execução da Azure AD. Para saber mais sobre o ID do cliente, consulte [os objetos principais de aplicação e serviço no Azure Ative Directory.](../active-directory/develop/app-objects-and-service-principals.md) 

As seguintes imagens mostram passos para registar uma aplicação web:

![Registar uma aplicação](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se registar a sua candidatura como uma aplicação nativa, pode especificar qualquer URI válido para o URI de redirecionamento. Para aplicações nativas, este valor não tem de ser um URL real. Para aplicações web, o URI de redirecionamento deve ser um URI válido, pois especifica o URL para o qual são fornecidos tokens.

Depois de ter registado a sua candidatura, verá o **ID de Aplicação (cliente)** em **Definições:**

![ID de aplicação do pedido registado](./media/authenticate-application/application-id.png)

Para obter mais informações sobre o registo de uma candidatura com a Azure AD, consulte [integrar aplicações com o Azure Ative Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Criar um segredo de cliente   
O pedido precisa de um segredo de cliente para provar a sua identidade ao pedir um token. Para adicionar o segredo do cliente, siga estes passos.

1. Navegue para o registo da sua aplicação no portal Azure.
1. Selecione a definição **de segredos & certificados.**
1. Sob **os segredos do Cliente,** selecione **Novo segredo de cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de validade desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é apresentado apenas uma vez.

    ![Segredo do cliente](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de clientes para aquisição simbólica  
Uma vez registado o seu pedido e concedido permissões para enviar/receber dados em Azure Event Hubs, pode adicionar código à sua aplicação para autenticar um principal de segurança e adquirir o token OAuth 2.0. Para autenticar e adquirir o token, pode utilizar uma das bibliotecas de [autenticação](../active-directory/develop/reference-v2-libraries.md) da plataforma de identidade da Microsoft ou outra biblioteca de código aberto que suporte o OpenID ou o Connect 1.0. A sua aplicação pode então utilizar o token de acesso para autorizar um pedido contra o Azure Event Hubs.

Para obter uma lista de cenários para os quais a aquisição de fichas é suportada, consulte a secção [de Cenários](https://aka.ms/msal-net-scenarios) da Biblioteca de Autenticação da [Microsoft (MSAL) para](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) o repositório .NET GitHub.

## <a name="samples"></a>Amostras
- [Amostras microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Estas amostras utilizam a antiga biblioteca **Microsoft.Azure.EventHubs,** mas pode atualizá-la facilmente para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.** Para mover a amostra da utilização da antiga biblioteca para uma nova, consulte o [Guia para migrar do Microsoft.Azure.EventHubs para Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Amostras de Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Esta amostra foi atualizada para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.**

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre o Azure RBAC, veja [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)](../role-based-access-control/overview.md)?
- Para aprender a atribuir e gerir atribuições de funções da Azure PowerShell, Azure CLI ou da REST API, consulte estes artigos:
    - [Utilizar o Azure PowerShell para adicionar ou remover atribuições de funções do Azure](../role-based-access-control/role-assignments-powershell.md)  
    - [Utilizar a CLI do Azure para adicionar ou remover atribuições de funções do Azure](../role-based-access-control/role-assignments-cli.md)
    - [Utilizar a API REST para adicionar ou remover atribuições de funções do Azure](../role-based-access-control/role-assignments-rest.md)
    - [Adicione atribuições de funções Azure usando modelos de Gestor de Recursos Azure](../role-based-access-control/role-assignments-template.md)

Consulte os seguintes artigos relacionados:
- [Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos Recursos dos Hubs de Eventos](authenticate-managed-identity.md)
- [Autenticar pedidos para Azure Event Hubs usando Assinaturas de Acesso Partilhado](authenticate-shared-access-signature.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory](authorize-access-azure-active-directory.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)
