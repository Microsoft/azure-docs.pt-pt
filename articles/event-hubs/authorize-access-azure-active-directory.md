---
title: Autorizar o acesso com o Azure Active Directory
description: Este artigo fornece informações sobre a autorização de acesso aos recursos do Event Hubs utilizando o Diretório Ativo Azure.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 6216b56c8e8f0de4f9cd60306bbf9c5ed49a11ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025208"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Autorizar acesso aos recursos do Event Hubs utilizando o Diretório Ativo Azure
O Azure Event Hubs apoia a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos de recursos do Event Hubs. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões a um diretor de segurança, que pode ser um utilizador, ou um diretor de serviço de aplicação. Para saber mais sobre papéis e atribuições de papéis, consulte [compreender os diferentes papéis.](../role-based-access-control/overview.md)

## <a name="overview"></a>Descrição geral
Quando um diretor de segurança (um utilizador, ou uma aplicação) tenta aceder a um recurso do Event Hubs, o pedido deve ser autorizado. Com a AD Azure, o acesso a um recurso é um processo em duas etapas. 

 1. Primeiro, a identidade do diretor de segurança é autenticada, e um token OAuth 2.0 é devolvido. O nome do recurso para `https://eventhubs.azure.net/`pedir um símbolo é . Para os clientes Kafka, o recurso `https://<namespace>.servicebus.windows.net`para solicitar um símbolo é.
 1. Em seguida, o símbolo é passado como parte de um pedido ao serviço De Eventos Hubs para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um pedido de pedido contenha um sinal de acesso OAuth 2.0 no prazo de execução. Se uma aplicação estiver a funcionar dentro de uma entidade Azure, como um Azure VM, um conjunto de escala de máquina virtual ou uma aplicação Azure Function, pode usar uma identidade gerida para aceder aos recursos. Para saber autenticar pedidos feitos por uma identidade gerida ao serviço De Eventos Hubs, consulte o [acesso authenticado aos recursos do Azure Event Hubs com o Azure Ative Directory e identidades geridas para a Azure Resources.](authenticate-managed-identity.md) 

O passo de autorização requer que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. O Azure Event Hubs fornece funções RBAC que englobam conjuntos de permissões para recursos do Event Hubs. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para obter mais informações sobre as funções RBAC, consulte [as funções RBAC incorporadas para o Azure Event Hubs](#built-in-rbac-roles-for-azure-event-hubs). 

Aplicações nativas e aplicações web que fazem pedidos para Centros de Eventos também podem autorizar com a Azure AD. Para saber como solicitar um token de acesso e usá-lo para autorizar pedidos de recursos do Event Hubs, consulte o [acesso authenticado aos Hubs de Eventos Azure com AD Azure a partir de uma aplicação](authenticate-application.md). 

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções rBAC para direitos de acesso
O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos garantidos através do [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md). O Azure Event Hubs define um conjunto de funções RBAC incorporadas que englobam conjuntos comuns de permissões usadas para aceder a dados do hub de eventos e também pode definir funções personalizadas para aceder aos dados.

Quando uma função RBAC é atribuída a um diretor de segurança da AD Azure, o Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser consultado ao nível de subscrição, ao grupo de recursos, ao espaço de nome sem nome do Event Hubs ou a qualquer recurso que lhe seja imputado. Um diretor de segurança da AD Azure pode ser um utilizador, ou um diretor de serviço de aplicação, ou uma [identidade gerida para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Funções RBAC incorporadas para Hubs de Eventos Azure
A Azure fornece as seguintes funções de RBAC incorporadopara autorizar o acesso aos dados do Event Hubs utilizando a Azure AD e a OAuth:

- [Azure Event Hubs Data owner](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Use this role to give complete access to Event Hubs resources.
- [Remetente de dados do Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)Data: Use esta função para dar acesso ao envio aos recursos do Event Hubs.
- Recetor de [dados do Azure Event Hubs:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)Utilize esta função para dar acesso aos recursos do Event Hubs.

## <a name="resource-scope"></a>Âmbito de recurso 
Antes de atribuir uma função RBAC a um diretor de segurança, determine o alcance de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o âmbito mais estreito possível.

A lista seguinte descreve os níveis a que pode aceder aos recursos do Event Hubs, começando pelo âmbito mais restrito:

- Grupo de **consumidores**: Neste âmbito, a atribuição de funções aplica-se apenas a esta entidade. Atualmente, o portal Azure não suporta atribuir um papel RBAC a um diretor de segurança a este nível. 
- Centro de **eventos**: A atribuição de funções aplica-se à entidade Do Hub de Eventos e ao grupo de consumidores ao seu abrigo.
- **Espaço de nome**: A atribuição de papéis abrange toda a topologia dos Centros de Eventos sob o espaço de nome e para o grupo de consumidores associado ao mesmo.
- **Grupo de recursos**: A atribuição de funções aplica-se a todos os recursos do Event Hubs no âmbito do grupo de recursos.
- **Subscrição**: A atribuição de funções aplica-se a todos os recursos do Event Hubs em todos os grupos de recursos na subscrição.

> [!NOTE]
> - Tenha em mente que as atribuições de funções RBAC podem demorar até cinco minutos para se propagar. 
> - Este conteúdo aplica-se tanto aos Centros de Eventos como aos Centros de Eventos para a Apache Kafka. Para obter mais informações sobre os Hubs de Eventos para suporte a Kafka, consulte Os Hubs de [Eventos para Kafka - segurança e autenticação.](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)


Para obter mais informações sobre como os papéis incorporados são definidos, consulte [compreender definições](../role-based-access-control/role-definitions.md#management-and-data-operations)de papéis . Para obter informações sobre a criação de funções RBAC personalizadas, consulte [Criar papéis personalizados para o Controlo de Acesso baseado em papel Azure](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Amostras
- [Microsoft.Azure.EventHubs amostras](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Estas amostras utilizam a antiga biblioteca **Microsoft.Azure.EventHubs,** mas pode facilmente atualizá-la para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.** Para mover a amostra da utilização da antiga biblioteca para uma nova, consulte o [Guia para migrar de Microsoft.Azure.EventHubs para Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Amostras de Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Esta amostra foi atualizada para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.**
- [Centros de eventos para amostras Kafka - OAuth.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) 


## <a name="next-steps"></a>Passos seguintes
- Saiba como atribuir uma função embutida no RBAC a um diretor de segurança, consulte o acesso authenticado aos recursos do Event Hubs utilizando o [Diretório Ativo Azure.](authenticate-application.md)
- Aprenda [a criar papéis personalizados com o RBAC.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)
- Saiba [como usar o Diretório Ativo Azure com o EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Consulte os seguintes artigos relacionados:

- [Autenticação solicita aos Hubs de Eventos Azure de uma aplicação que usa o Diretório Ativo Azure](authenticate-application.md)
- [Autenticar uma identidade gerida com o Diretório Ativo Azure para aceder aos Recursos hubs do Evento](authenticate-managed-identity.md)
- [Autenticar pedidos aos Hubs de Eventos Azure usando assinaturas de acesso partilhado](authenticate-shared-access-signature.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)
