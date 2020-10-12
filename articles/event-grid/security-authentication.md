---
title: Autenticar a entrega do evento aos manipuladores de eventos (Azure Event Grid)
description: Este artigo descreve diferentes formas de autenticar a entrega aos manipuladores de eventos na Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: abe16c9598c8c10caa832150aafac997dd7f1624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87460648"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Autenticar a entrega do evento aos manipuladores de eventos (Azure Event Grid)
Este artigo fornece informações sobre a autenticação da entrega de eventos aos manipuladores de eventos. Também mostra como proteger os pontos finais do webhook que são usados para receber eventos da Grade de Eventos usando O Diretório Ativo Azure (Azure AD) ou um segredo partilhado.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Utilizar identidades atribuídas ao sistema para a entrega de eventos
Você pode ativar uma identidade gerida atribuída ao sistema para um tópico ou domínio e usar a identidade para encaminhar eventos para destinos apoiados, tais como filas e tópicos de Service Bus, centros de eventos e contas de armazenamento.

Eis os passos: 

1. Crie um tópico ou domínio com uma identidade atribuída ao sistema, ou atualize um tópico ou domínio existente para permitir a identidade. 
1. Adicione a identidade a um papel apropriado (por exemplo, Service Bus Data Sender) no destino (por exemplo, uma fila de autocarros de serviço).
1. Ao criar subscrições de eventos, permita o uso da identidade para entregar eventos ao destino. 

Para obter instruções detalhadas passo a passo, consulte [a entrega do Evento com uma identidade gerida](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autenticar entrega de eventos para pontos finais webhook
As secções seguintes descrevem como autenticar a entrega do evento nos pontos finais webhook. Tem de utilizar um mecanismo de aperto de mão de validação, independentemente do método utilizado. Consulte a [entrega do evento Webhook](webhook-event-delivery.md) para obter mais detalhes. 


### <a name="using-azure-active-directory-azure-ad"></a>Utilizando o Diretório Ativo Azure (Azure AD)
Você pode proteger o ponto final webhook que é usado para receber eventos da Grade de Eventos usando Azure AD. Você precisará criar uma aplicação AD Azure, criar uma função e principal de serviço na sua aplicação autorizando a Grade de Eventos, e configurar a subscrição do evento para usar a aplicação AZure AD. Saiba como [configurar o Azure Ative Directory com a Grade de Eventos.](secure-webhook-delivery.md)

### <a name="using-client-secret-as-a-query-parameter"></a>Usando o segredo do cliente como parâmetro de consulta
Também pode proteger o seu ponto final webhook adicionando parâmetros de consulta ao URL de destino webhook especificado como parte da criação de uma Subscrição de Eventos. Desacorde um dos parâmetros de consulta para ser um segredo de cliente, como um [token de acesso](https://en.wikipedia.org/wiki/Access_token) ou um segredo partilhado. O serviço 'Grade de Eventos' inclui todos os parâmetros de consulta em cada pedido de entrega de eventos ao webhook. O serviço webhook pode recuperar e validar o segredo. Se o segredo do cliente for atualizado, a subscrição do evento também precisa de ser atualizada. Para evitar falhas de entrega durante esta rotação secreta, faça com que o webhook aceite segredos antigos e novos por uma duração limitada antes de atualizar a subscrição do evento com o novo segredo. 

Como os parâmetros de consulta podem conter segredos de clientes, eles são tratados com cuidado extra. São armazenados como encriptados e não são acessíveis aos operadores de serviço. Não são registados como parte dos registos/vestígios de serviço. Ao recuperar as propriedades de Subscrição de Eventos, os parâmetros de consulta de destino não são devolvidos por padrão. For example: [--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parameter is to be used in Azure [CLI](/cli/azure?view=azure-cli-latest).

Para obter mais informações sobre a entrega de eventos a webhooks, consulte [a entrega do evento Webhook](webhook-event-delivery.md)

> [!IMPORTANT]
A Azure Event Grid suporta apenas pontos finais **https** webhook. 


## <a name="next-steps"></a>Passos seguintes
Consulte [clientes editoriais Authenticate](security-authenticate-publishing-clients.md) para saber sobre autenticar clientes que publicam eventos em tópicos ou domínios. 
