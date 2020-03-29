---
title: Identidades geridas para recursos Azure com Ônibus de Serviço
description: Este artigo descreve como usar identidades geridas para aceder a entidades azure service bus (filas, tópicos e subscrições).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 89de6bf80d14ec77fe6b1f98b6e1d15c6e573fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756288"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autenticar uma identidade gerida com o Diretório Ativo Azure para aceder aos recursos do Azure Service Bus
[Identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) é uma funcionalidade cross-Azure que lhe permite criar uma identidade segura associada à implementação sob a qual o seu código de aplicação é executado. Pode então associar essa identidade a funções de controlo de acesso que concedem permissões personalizadas para aceder a recursos específicos do Azure de que a sua aplicação necessita.

Com identidades geridas, a plataforma Azure gere esta identidade de tempo de corrido. Não precisa de armazenar e proteger as chaves de acesso no seu código de aplicação ou configuração, quer para a identidade em si, quer para os recursos a que necessita aceder. Uma aplicação de cliente de ônibus de serviço que funciona dentro de uma aplicação Azure App Service ou numa máquina virtual com entidades geridas ativadas para suporte a recursos Azure não precisa de lidar com as regras e chaves SAS, ou quaisquer outros tokens de acesso. A aplicação do cliente só precisa do endereço final do espaço de nome de Mensagens de Autocarro de Serviço. Quando a aplicação se conecta, o Service Bus liga o contexto da entidade gerida ao cliente numa operação que é mostrada num exemplo mais tarde neste artigo. Uma vez associado a uma identidade gerida, o seu cliente De ônibus de serviço pode fazer todas as operações autorizadas. A autorização é concedida associando uma entidade gerida com funções de Ônibus de serviço. 

## <a name="overview"></a>Descrição geral
Quando um diretor de segurança (utilizador, grupo ou aplicação) tenta aceder a uma entidade do Ônibus de serviço, o pedido deve ser autorizado. Com a AD Azure, o acesso a um recurso é um processo em duas etapas. 

 1. Primeiro, a identidade do diretor de segurança é autenticada, e um token OAuth 2.0 é devolvido. O nome do recurso para `https://servicebus.azure.net`pedir um símbolo é .
 1. Em seguida, o símbolo é passado como parte de um pedido ao serviço de ônibus de serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um pedido de pedido contenha um sinal de acesso OAuth 2.0 no prazo de execução. Se uma aplicação estiver a funcionar dentro de uma entidade Azure, como um Azure VM, um conjunto de escala de máquina virtual ou uma aplicação Azure Function, pode usar uma identidade gerida para aceder aos recursos. 

O passo de autorização requer que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. O Azure Service Bus fornece funções RBAC que englobam conjuntos de permissões para recursos de ônibus de serviço. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para saber mais sobre a atribuição de funções RBAC ao Azure Service Bus, consulte [as funções RBAC incorporadas para o Azure Service Bus.](#built-in-rbac-roles-for-azure-service-bus) 

Aplicações nativas e aplicações web que fazem pedidos para O Ônibus de Serviço também podem autorizar com a Azure AD. Este artigo mostra-lhe como solicitar um sinal de acesso e usá-lo para autorizar pedidos de recursos de ônibus de serviço. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Atribuição de funções RBAC para direitos de acesso
O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos garantidos através do [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md). O Azure Service Bus define um conjunto de funções RBAC incorporadas que englobam conjuntos comuns de permissões usadas para aceder a entidades de ônibus de serviço e também pode definir funções personalizadas para aceder aos dados.

Quando uma função RBAC é atribuída a um diretor de segurança da AD Azure, o Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser consultado ao nível de subscrição, ao grupo de recursos ou ao espaço de nome do Bus de Serviço. Um diretor de segurança da AD Azure pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma identidade gerida para os recursos Azure.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Funções RBAC incorporadas para ônibus de serviço Azure
Para o Azure Service Bus, a gestão de espaços de nome e todos os recursos conexos através do portal Azure e da API de gestão de recursos Azure já está protegida utilizando o modelo de controlo de acesso baseado *em funções* (RBAC). O Azure fornece as funções RBAC incorporadas abaixo para autorizar o acesso a um espaço de nome de autocarro de serviço:

- [Azure Service Bus Data Owner](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Permite o acesso de dados ao espaço de nome do Bus de Serviço e às suas entidades (filas, tópicos, subscrições e filtros)
- Remetente de dados do [ônibus azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)service : Use esta função para dar acesso ao espaço de nome do Bus de Serviço e suas entidades.
- Recetor de dados de [ônibus de serviço Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Use esta função para dar acesso ao espaço de nome do Bus de Serviço e suas entidades. 

## <a name="resource-scope"></a>Âmbito de recurso 
Antes de atribuir uma função RBAC a um diretor de segurança, determine o alcance de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o âmbito mais estreito possível.

A lista seguinte descreve os níveis a que pode aceder aos recursos do Ônibus de serviço, começando pelo âmbito mais restrito:

- **Fila,** **tópico,** ou **subscrição**: Atribuição de funções aplica-se à entidade específica do Ônibus de Serviço. Atualmente, o portal Azure não suporta atribuir utilizadores/grupos/identidades geridas às funções De Serviço de RBAC ao nível da subscrição. Aqui está um exemplo de utilização do comando Azure CLI: [az-role-assignment-create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) para atribuir uma identidade a uma função RBAC de ônibus de serviço: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus namespace**: A atribuição de funções abrange toda a topologia do Service Bus sob o espaço de nome e para o grupo de consumidores associado ao mesmo.
- **Grupo de recursos**: A atribuição de funções aplica-se a todos os recursos do Service Bus no âmbito do grupo de recursos.
- **Subscrição**: A atribuição de funções aplica-se a todos os recursos do Service Bus em todos os grupos de recursos na subscrição.

> [!NOTE]
> Tenha em mente que as atribuições de funções RBAC podem demorar até cinco minutos para se propagar. 

Para obter mais informações sobre como os papéis incorporados são definidos, consulte [compreender definições](../role-based-access-control/role-definitions.md#management-and-data-operations)de papéis . Para obter informações sobre a criação de funções RBAC personalizadas, consulte [Criar papéis personalizados para o Controlo de Acesso baseado em papel Azure](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Ativar identidades geridas num VM
Antes de poder utilizar identidades geridas para a Azure Resources para autorizar recursos de ônibus de serviço a partir do seu VM, você deve primeiro ativar identidades geridas para os Recursos Azure no VM. Para aprender a ativar identidades geridas para os Recursos Azure, consulte um destes artigos:

- [Portal Azure](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes do Gestor de Recursos Azure](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerida em Azure AD
Para autorizar um pedido ao serviço de ônibus de serviço a partir de uma identidade gerida na sua aplicação, primeiro configurar as definições de controlo de acesso baseado em funções (RBAC) para essa identidade gerida. O Azure Service Bus define funções RBAC que englobam permissões para envio e leitura a partir de Service Bus. Quando a função RBAC é atribuída a uma identidade gerida, a identidade gerida tem acesso às entidades de ônibus de serviço no âmbito adequado.

Para mais informações sobre a atribuição de funções RBAC, consulte [Authenticate e autorize com o Diretório Ativo azure o acesso aos recursos do Ônibus de serviço.](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus)

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Use service bus com identidades geridas para recursos Azure
Para utilizar o Service Bus com identidades geridas, é necessário atribuir a identidade ao papel e ao âmbito adequado. O procedimento nesta secção utiliza uma aplicação simples que funciona sob uma identidade gerida e acede aos recursos do Bus service.

Aqui estamos usando uma aplicação web de amostra alojada no [Azure App Service](https://azure.microsoft.com/services/app-service/). Para instruções passo a passo para criar uma aplicação web, consulte [Criar uma aplicação web core ASP.NET em Azure](../app-service/app-service-web-get-started-dotnet.md)

Uma vez criada a aplicação, siga estes passos: 

1. Vá a **Definições** e selecione **Identidade**. 
1. Selecione o **Estado** a **estar ligado**. . 
1. Selecione **Guardar** para guardar a definição. 

    ![Identidade gerida para uma aplicação web](./media/service-bus-managed-service-identity/identity-web-app.png)

Uma vez ativado esta configuração, uma nova identidade de serviço é criada no seu Diretório Ativo Azure (Azure AD) e configurada no anfitrião do Serviço de Aplicações.

Agora, atribua esta identidade de serviço a um papel no âmbito exigido nos recursos do seu Ônibus de serviço.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções RBAC utilizando o portal Azure
Para atribuir uma função a um espaço de nome de ônibus de serviço, navegue para o espaço de nome no portal Azure. Mostrar as definições de Controlo de Acesso (IAM) para o recurso e seguir estas instruções para gerir as atribuições de funções:

> [!NOTE]
> Os seguintes passos atribuem uma função de identidade de serviço aos seus espaços de nome sinuosos de ônibus de serviço. Pode seguir os mesmos passos para atribuir uma função a outros âmbitos suportados (grupo de recursos e subscrição). 
> 
> [Crie um espaço de nome de mensagens de ônibus de serviço](service-bus-create-namespace-portal.md) se não tiver um. 

1. No portal Azure, navegue para o seu espaço de nome do Bus de Serviço e exiba a **visão geral** para o espaço de nome. 
1. Selecione **Access Control (IAM)** no menu esquerdo para visualizar as definições de controlo de acesso para o espaço de nome do Bus de Serviço.
1.  Selecione o separador de **atribuições de funções** para ver a lista de atribuições de papéis.
3.  Selecione **Adicionar** para adicionar um novo papel.
4.  Na página de atribuição de **funções Add,** selecione as funções de Ônibus de Serviço Azure que pretende atribuir. Em seguida, procure localizar a identidade de serviço que tinha registado para atribuir o papel.
    
    ![Adicionar página de atribuição de papéis](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Selecione **Guardar**. A identidade a quem atribuiu o papel aparece listada nesse papel. Por exemplo, a imagem que se segue mostra que a identidade de serviço tem o proprietário de Dados de Ônibus de Serviço Azure.
    
    ![Identidade atribuída a um papel](./media/service-bus-managed-service-identity/role-assigned.png)

Uma vez atribuído o papel, a aplicação web terá acesso às entidades do Ônibus de serviço sob o âmbito definido. 

### <a name="run-the-app"></a>Executar a aplicação

Agora, modifique a página predefinida da aplicação ASP.NET que criou. Pode utilizar o código de aplicação web [a partir deste repositório GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

A página Default.aspx é a sua página de aterragem. O código pode ser encontrado no ficheiro Default.aspx.cs. O resultado é uma aplicação web mínima com alguns campos de entrada, e com botões **de envio** e **receção** que ligam ao Service Bus para enviar ou receber mensagens.

Note como o objeto da Fábrica de [Mensagens](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inicializado. Em vez de utilizar o fornecedor de fichas de acesso partilhado (SAS), o `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` código cria um fornecedor simbólico para a identidade gerida com a chamada. Como tal, não há segredos para reter e usar. O fluxo do contexto de identidade gerido para o Service Bus e o aperto de mão de autorização são automaticamente tratados pelo fornecedor simbólico. É um modelo mais simples do que usar sas.

Depois de efazer estas alterações, publique e execute a aplicação. Pode obter facilmente os dados de publicação corretos, descarregando e importando um perfil editorial no Visual Studio:

![Obtenha perfil de publicação](./media/service-bus-managed-service-identity/msi3.png)
 
Para enviar ou receber mensagens, insira o nome do espaço de nome e o nome da entidade que criou. Em seguida, clique em **enviar** ou **receber**.


> [!NOTE]
> - A identidade gerida funciona apenas dentro do ambiente Azure, em serviços de Aplicações, VMs Azure e conjuntos de escala. Para aplicações .NET, a biblioteca Microsoft.Azure.Services.Services.AppAuthentication, que é utilizada pelo pacote NuGet de Ônibus de Serviço, fornece uma abstração sobre este protocolo e suporta uma experiência de desenvolvimento local. Esta biblioteca também permite testar o seu código localmente na sua máquina de desenvolvimento, utilizando a sua conta de utilizador do Visual Studio, Azure CLI 2.0 ou Ative Directory Integrated Authentication. Para mais informações sobre as opções de desenvolvimento local com esta biblioteca, consulte a [autenticação Service-to-service para o Azure Key Vault utilizando .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Atualmente, as identidades geridas não funcionam com ranhuras de implementação do Serviço de Aplicações.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
