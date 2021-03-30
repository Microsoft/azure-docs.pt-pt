---
title: Autorizar o acesso com o Azure Active Directory
description: Este artigo fornece informações sobre a autorização de acesso aos recursos do Event Hubs utilizando o Azure Ative Directory.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: d794b03fdbb5429983788c74cbb05a7c13bf2d76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910802"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory
O Azure Event Hubs suporta a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos aos recursos do Event Hubs. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (Azure RBAC) para conceder permissões a um principal de segurança, que pode ser um utilizador ou um diretor de serviço de aplicação. Para saber mais sobre papéis e atribuições de papéis, consulte [compreender os diferentes papéis.](../role-based-access-control/overview.md)

## <a name="overview"></a>Descrição Geral
Quando um diretor de segurança (um utilizador ou uma aplicação) tenta aceder a um recurso do Event Hubs, o pedido deve ser autorizado. Com o Azure AD, o acesso a um recurso é um processo em duas etapas. 

 1. Primeiro, a identidade do diretor de segurança é autenticada, e um token OAuth 2.0 é devolvido. O nome de recurso para solicitar um token é `https://eventhubs.azure.net/` , e é o mesmo para todas as nuvens/inquilinos. Para os clientes kafka, o recurso para solicitar um token é `https://<namespace>.servicebus.windows.net` .
 1. Em seguida, o token é passado como parte de um pedido ao serviço Event Hubs para autorizar o acesso ao recurso especificado.

O passo de autenticação requer que um pedido de candidatura contenha um token de acesso OAuth 2.0 no tempo de execução. Se uma aplicação estiver a ser executada dentro de uma entidade Azure, como um Azure VM, um conjunto de escala de máquina virtual ou uma aplicação Azure Function, pode usar uma identidade gerida para aceder aos recursos. Para aprender a autenticar pedidos feitos por uma identidade gerida ao serviço Event Hubs, consulte [autenticar o acesso aos recursos do Azure Event Hubs com o Azure Ative Directory e identidades geridas para a Azure Resources.](authenticate-managed-identity.md) 

O passo de autorização requer que uma ou mais funções da Azure sejam atribuídas ao diretor de segurança. O Azure Event Hubs fornece funções Azure que englobam conjuntos de permissões para recursos de Event Hubs. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para obter mais informações sobre os papéis do [Azure, consulte os papéis incorporados do Azure para o Azure Event Hubs](#azure-built-in-roles-for-azure-event-hubs). 

Aplicações nativas e aplicações web que fazem pedidos para Os Centros de Eventos também podem autorizar com a Azure AD. Para saber como solicitar um token de acesso e usá-lo para autorizar pedidos de recursos do Event Hubs, consulte [autenticar o acesso aos Hubs de Eventos Azure com Azure AD a partir de uma aplicação.](authenticate-application.md) 

## <a name="assign-azure-roles-for-access-rights"></a>Atribuir funções da Azure para direitos de acesso
O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos seguros através do [controlo de acesso baseado em funções da Azure (Azure RBAC)](../role-based-access-control/overview.md). O Azure Event Hubs define um conjunto de funções incorporadas do Azure que englobam conjuntos comuns de permissões usadas para aceder a dados do centro de eventos e também pode definir funções personalizadas para aceder aos dados.

Quando um papel de Azure é atribuído a um diretor de segurança da AZure, a Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser analisado ao nível de subscrição, ao grupo de recursos, ao espaço de nomes do Event Hubs ou a qualquer recurso sob o mesmo. Um diretor de segurança Azure AD pode ser um utilizador, ou um diretor de serviço de aplicação, ou uma [identidade gerida para os recursos da Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Azure papéis incorporados para Azure Event Hubs
A Azure fornece as seguintes funções incorporadas do Azure para autorizar o acesso aos dados do Event Hubs utilizando Azure AD e OAuth:

| Função | Description | 
| ---- | ----------- | 
| [Proprietário de dados Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) | Use esta função para dar acesso completo aos recursos do Event Hubs. |
| [Remetente de dados do Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) | Use esta função para dar acesso ao envio aos recursos do Event Hubs. |
| [Recetor de dados Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) | Utilize esta função para dar acesso ao consumo/receção aos recursos do Event Hubs. |

Para funções incorporadas no Registo de Schema, consulte [as funções de Registo de Schema](schema-registry-overview.md#azure-role-based-access-control).

## <a name="resource-scope"></a>Âmbito do recurso 
Antes de atribuir um papel de Azure a um diretor de segurança, determine o âmbito de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o âmbito mais estreito possível.

A lista a seguir descreve os níveis em que pode estender o acesso aos recursos do Event Hubs, começando pelo âmbito mais restrito:

- **Grupo de consumidores**: Neste âmbito, a atribuição de funções aplica-se apenas a esta entidade. Atualmente, o portal Azure não suporta a atribuição de um papel de Azure a um diretor de segurança a este nível. 
- **Centro de eventos**: A atribuição de funções aplica-se à entidade Event Hub e ao grupo de consumidores ao seu abrigo.
- **Namespace**: A atribuição de funções abrange toda a topologia dos Centros de Eventos sob o espaço de nome e para o grupo de consumidores que lhe está associado.
- **Grupo de recursos**: A atribuição de funções aplica-se a todos os recursos do Event Hubs no âmbito do grupo de recursos.
- **Subscrição**: A atribuição de funções aplica-se a todos os recursos do Event Hubs em todos os grupos de recursos da subscrição.

> [!NOTE]
> - Tenha em mente que as atribuições de funções do Azure podem demorar até cinco minutos a propagar-se. 
> - Este conteúdo aplica-se tanto a Centros de Eventos como a Centros de Eventos para Apache Kafka. Para obter mais informações sobre os Centros de Eventos para suporte a Kafka, consulte [Os Centros de Eventos para Kafka - segurança e autenticação.](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)


Para obter mais informações sobre como as funções incorporadas são [definidas, consulte as definições de funções](../role-based-access-control/role-definitions.md#management-and-data-operations)de entender. Para obter informações sobre a criação de funções personalizadas [Azure, consulte os papéis personalizados da Azure](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Amostras
- [Amostras microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Estas amostras utilizam a antiga biblioteca **Microsoft.Azure.EventHubs,** mas pode atualizá-la facilmente para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.** Para mover a amostra da utilização da antiga biblioteca para uma nova, consulte o [Guia para migrar do Microsoft.Azure.EventHubs para Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Amostras de Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Esta amostra foi atualizada para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.**
- [Centros de Eventos para amostras kafka - OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Passos seguintes
- Saiba como atribuir um papel incorporado a um diretor de segurança, consulte [o Acesso autenticado aos recursos do Event Hubs utilizando o Azure Ative Directory](authenticate-application.md).
- Saiba [como criar papéis personalizados com o Azure RBAC.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)
- Saiba [como usar o Azure Ative Directory com EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Consulte os seguintes artigos relacionados:

- [Autenticar pedidos aos Azure Event Hubs a partir de uma aplicação utilizando o Azure Ative Directory](authenticate-application.md)
- [Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos Recursos dos Hubs de Eventos](authenticate-managed-identity.md)
- [Autenticar pedidos para Azure Event Hubs usando Assinaturas de Acesso Partilhado](authenticate-shared-access-signature.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)
