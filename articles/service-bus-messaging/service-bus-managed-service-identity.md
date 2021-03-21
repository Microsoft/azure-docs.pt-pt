---
title: Identidades geridas para recursos Azure com Service Bus
description: Este artigo descreve como usar identidades geridas para aceder a entidades do Azure Service Bus (filas, tópicos e subscrições).
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: bd985acd9b775d6baef0abf488952e28c17aef2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954315"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos recursos do Azure Service Bus
[As identidades geridas para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) são uma funcionalidade cross-Azure que lhe permite criar uma identidade segura associada à implementação sob a qual o seu código de aplicação é executado. Em seguida, pode associar essa identidade a funções de controlo de acesso que concedem permissões personalizadas para aceder a recursos específicos do Azure de que a sua aplicação necessita.

Com identidades geridas, a plataforma Azure gere esta identidade de tempo de execução. Não precisa de armazenar e proteger as chaves de acesso no seu código de aplicação ou configuração, quer para a própria identidade, quer para os recursos a que necessita. Uma aplicação de clientes Service Bus que funciona dentro de uma aplicação do Azure App Service ou numa máquina virtual com entidades geridas ativadas para suporte a recursos Azure não precisa de lidar com as regras e chaves da SAS, ou quaisquer outros tokens de acesso. A aplicação do cliente só precisa do endereço final do espaço de nome de mensagens de serviço. Quando a aplicação se conecta, a Service Bus liga o contexto da entidade gerida ao cliente numa operação que é mostrada num exemplo mais tarde neste artigo. Uma vez associado a uma identidade gerida, o seu cliente Service Bus pode fazer todas as operações autorizadas. A autorização é concedida associando uma entidade gerida com funções de Service Bus. 

## <a name="overview"></a>Descrição geral
Quando um principal de segurança (um utilizador, grupo ou aplicação) tenta aceder a uma entidade do Service Bus, o pedido deve ser autorizado. Com o Azure AD, o acesso a um recurso é um processo em duas etapas. 

 1. Primeiro, a identidade do diretor de segurança é autenticada, e um token OAuth 2.0 é devolvido. O nome do recurso para solicitar um token é `https://servicebus.azure.net` .
 1. Em seguida, o token é passado como parte de um pedido ao serviço service bus para autorizar o acesso ao recurso especificado.

O passo de autenticação requer que um pedido de candidatura contenha um token de acesso OAuth 2.0 no tempo de execução. Se uma aplicação estiver a ser executada dentro de uma entidade Azure, como um Azure VM, um conjunto de escala de máquina virtual ou uma aplicação Azure Function, pode usar uma identidade gerida para aceder aos recursos. 

O passo de autorização requer que uma ou mais funções da Azure sejam atribuídas ao diretor de segurança. A Azure Service Bus fornece funções Azure que englobam conjuntos de permissões para recursos de Service Bus. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para saber mais sobre a atribuição de papéis da Azure service bus, consulte [as funções incorporadas da Azure Service Bus.](#azure-built-in-roles-for-azure-service-bus) 

As aplicações nativas e aplicações web que fazem pedidos para Service Bus também podem autorizar com a Azure AD. Este artigo mostra-lhe como solicitar um token de acesso e usá-lo para autorizar pedidos de recursos de Service Bus. 


## <a name="assigning-azure-roles-for-access-rights"></a>Atribuir funções da Azure para direitos de acesso
O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos seguros através do [controlo de acesso baseado em funções da Azure (Azure RBAC)](../role-based-access-control/overview.md). A Azure Service Bus define um conjunto de funções incorporadas Azure que englobam conjuntos comuns de permissões usadas para aceder a entidades de Service Bus e também pode definir funções personalizadas para aceder aos dados.

Quando um papel de Azure é atribuído a um diretor de segurança da AZure, a Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser alargado ao nível de subscrição, ao grupo de recursos ou ao espaço de nomes do Service Bus. Um diretor de segurança Azure AD pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma identidade gerida para os recursos da Azure.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Azure funções incorporadas para Azure Service Bus
Para a Azure Service Bus, a gestão de espaços de nome e todos os recursos relacionados através do portal Azure e da gestão de recursos AZure API já está protegida usando o modelo Azure RBAC. A Azure fornece as funções incorporadas abaixo do Azure para autorizar o acesso a um espaço de nomes de Autocarros de Serviço:

- [Azure Service Bus Data Owner](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Permite o acesso de dados ao espaço de nomes do Service Bus e das suas entidades (filas, tópicos, subscrições e filtros)
- [Azure Service Bus Data Sender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Use esta função para dar acesso ao espaço de nomes do Service Bus e das suas entidades.
- [Azure Service Bus Data Receiver](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Use esta função para dar acesso ao espaço de nomes do Service Bus e das suas entidades. 

## <a name="resource-scope"></a>Âmbito do recurso 
Antes de atribuir um papel de Azure a um diretor de segurança, determine o âmbito de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o âmbito mais estreito possível.

A lista a seguir descreve os níveis em que pode aceder aos recursos do Service Bus, começando pelo âmbito mais restrito:

- **Fila,** **tópico**, ou **subscrição**: A atribuição de funções aplica-se à entidade específica do Service Bus. Atualmente, o portal Azure não suporta a atribuição de utilizadores/grupos/identidades geridas às funções de Service Bus Azure ao nível da subscrição. Aqui está um exemplo de utilização do comando Azure CLI: [az-role-assignment-create](/cli/azure/role/assignment?#az-role-assignment-create) para atribuir uma identidade a um papel de Service Bus Azure: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Espaço de nome do Service Bus**: A atribuição de funções abrange toda a topologia do Service Bus sob o espaço de nome e para o grupo de consumidores que lhe está associado.
- **Grupo de recursos**: A atribuição de funções aplica-se a todos os recursos do Service Bus no âmbito do grupo de recursos.
- **Subscrição**: A atribuição de funções aplica-se a todos os recursos do Service Bus em todos os grupos de recursos da subscrição.

> [!NOTE]
> Tenha em mente que as atribuições de funções do Azure podem demorar até cinco minutos a propagar-se. 

Para obter mais informações sobre como as funções incorporadas são [definidas, consulte as definições de funções](../role-based-access-control/role-definitions.md#management-and-data-operations)de entender. Para obter informações sobre a criação de funções personalizadas [Azure, consulte os papéis personalizados da Azure](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Permitir identidades geridas num VM
Antes de utilizar identidades geridas para a Azure Resources para autorizar os recursos do Service Bus a partir do seu VM, tem primeiro de ativar identidades geridas para recursos Azure no VM. Para aprender como permitir identidades geridas para recursos Azure, consulte um destes artigos:

- [Portal do Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerida em Azure AD
Para autorizar um pedido ao serviço service bus a partir de uma identidade gerida na sua aplicação, configurar primeiro as definições de controlo de acesso baseado em funções (Azure RBAC) para essa identidade gerida. A Azure Service Bus define funções Azure que englobam permissões para envio e leitura da Service Bus. Quando o papel de Azure é atribuído a uma identidade gerida, a identidade gerida é concedida acesso a entidades de Service Bus no âmbito adequado.

Para obter mais informações sobre a atribuição de funções Azure, consulte [Authenticate e autorize com a Azure Ative Directory para acesso aos recursos do Service Bus.](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Use Service Bus com identidades geridas para recursos Azure
Para utilizar o Service Bus com identidades geridas, tem de atribuir à identidade o papel e o âmbito adequado. O procedimento nesta secção utiliza uma aplicação simples que funciona sob uma identidade gerida e acessa recursos de Service Bus.

Aqui estamos a usar uma aplicação web de amostras hospedada no [Azure App Service](https://azure.microsoft.com/services/app-service/). Para obter instruções passo a passo para criar uma aplicação web, consulte [Criar uma aplicação web core ASP.NET em Azure](../app-service/quickstart-dotnetcore.md)

Assim que a aplicação for criada, siga estes passos: 

1. Vá a **Definições** e selecione **Identidade**. 
1. Selecione o **Estado** a ser **ligado**. 
1. Selecione **Guardar** para guardar a definição. 

    ![Identidade gerida para uma aplicação web](./media/service-bus-managed-service-identity/identity-web-app.png)

Uma vez ativada esta definição, é criada uma nova identidade de serviço no seu Azure Ative Directory (Azure AD) e configurada no anfitrião do Serviço de Aplicações.

> [!NOTE]
> Quando utilizar uma identidade gerida, a cadeia de ligação deve estar no formato: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=Managed Identity` .

Agora, atribua esta identidade de serviço a um papel no âmbito exigido nos seus recursos de Service Bus.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Atribuir funções de Azure utilizando o portal Azure
Para atribuir uma função a um espaço de nomes de Service Bus, navegue para o espaço de nomes no portal Azure. Mostrar as definições do Controlo de Acesso (IAM) para o recurso e seguir estas instruções para gerir as atribuições de funções:

> [!NOTE]
> Os passos seguintes atribuem uma função de identidade de serviço aos espaços de nome do Service Bus. Pode seguir os mesmos passos para atribuir uma função em outros âmbitos suportados (grupo de recursos e subscrição). 
> 
> [Crie um espaço de nome de mensagens de autocarro de serviço](service-bus-create-namespace-portal.md) se não tiver um. 

1. No portal Azure, navegue no seu espaço de nomes do Service Bus e apresente a **Visão Geral** para o espaço de nomes. 
1. Selecione **Access Control (IAM)** no menu esquerdo para exibir as definições de controlo de acesso para o espaço de nomes do Service Bus.
1.  Selecione o **separador funções** para ver a lista de atribuições de funções.
3.  **Selecione Adicionar** e, em seguida, selecione **Adicionar a atribuição de função**.
4.  Na página **de atribuição de funções Adicionar,** siga estes passos:
    1. Para **Role**, selecione a função Service Bus que pretende atribuir. Neste exemplo, é dono de dados de autocarros da **Azure Service.**
    1. Para o acesso ao campo **de atribuição,** selecione **o Serviço de Aplicações** em **Sistema atribuído à identidade gerida.** 
    1. Selecione a **subscrição** na qual foi criada a identidade gerida para a aplicação web.
    1. Selecione a **identidade gerida** para a aplicação web que criou. O nome padrão para a identidade é o mesmo que o nome da aplicação web. 
    1. Em seguida, **selecione Save**.
        
        ![Adicionar página de atribuição de funções](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    Uma vez atribuído o papel, a aplicação web terá acesso às entidades do Service Bus sob o âmbito definido. 

    > [!NOTE]
    > Para uma lista de serviços que suportam identidades geridas, consulte [serviços que suportem identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="run-the-app"></a>Executar a aplicação
Agora, modifique a página predefinição da aplicação ASP.NET que criou. Pode utilizar o código de aplicação web a partir [deste repositório GitHub.](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)  

A página padrão.aspx é a sua página de aterragem. O código pode ser encontrado no ficheiro Predefinido.aspx.cs. O resultado é uma aplicação web mínima com alguns campos de entrada, e com botões **de envio** e **receção** que se conectam ao Service Bus para enviar ou receber mensagens.

Note como o objeto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inicializado. Em vez de utilizar o fornecedor de token de acesso partilhado (SAS), o código cria um fornecedor simbólico para a identidade gerida com a `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` chamada. Como tal, não há segredos para reter e usar. O fluxo do contexto de identidade gerido para Service Bus e o aperto de mão de autorização são automaticamente manuseados pelo provedor de fichas. É um modelo mais simples do que usar SAS.

Depois de estojorar estas alterações, publique e execute a aplicação. Pode obter facilmente os dados de publicação corretos, descarregando e importando um perfil de publicação no Visual Studio:

![Obtenha o perfil de publicação](./media/service-bus-managed-service-identity/msi3.png)
 
Para enviar ou receber mensagens, insira o nome do espaço de nome e o nome da entidade que criou. Em seguida, clique em **enviar** ou **receber.**


> [!NOTE]
> - A identidade gerida funciona apenas dentro do ambiente Azure, em serviços de Aplicações, VMs Azure e conjuntos de escala. Para aplicações .NET, a biblioteca Microsoft.Azure.Services.AppAuthentication, que é utilizada pelo pacote Service Bus NuGet, fornece uma abstração sobre este protocolo e suporta uma experiência de desenvolvimento local. Esta biblioteca também permite testar o seu código localmente na sua máquina de desenvolvimento, utilizando a sua conta de utilizador a partir do Visual Studio, Azure CLI 2.0 ou Ative Directory Integrated Authentication. Para obter mais informações sobre as opções de desenvolvimento local com esta biblioteca, consulte [a autenticação de serviço-a-serviço para Azure Key Vault utilizando .NET](/dotnet/api/overview/azure/service-to-service-authentication).  

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
