---
title: Tópicos do sistema na Grelha de Eventos Azure
description: Descreve tópicos do sistema na Grelha de Eventos Azure.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: b1fbecb1e372602f9c252d43d2a1f93524ef1846
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052970"
---
# <a name="system-topics-in-azure-event-grid"></a>Tópicos do sistema na Grelha de Eventos Azure
Um tópico de sistema em Event Grid representa um ou mais eventos publicados pelos serviços da Azure, tais como Azure Storage e Azure Event Hubs. Por exemplo, um tópico do sistema pode representar **todos os eventos blob** ou apenas eventos **blob criados** e **blob eliminados** publicados para uma **conta de armazenamento específica**. Neste exemplo, quando uma bolha é carregada para a conta de armazenamento, o serviço Azure Storage publica um evento **criado blob** para o tópico do sistema em Event Grid, que depois encaminha o evento para [os subscritores](event-handlers.md) do tópico que recebem e processam o evento. 

> [!NOTE] 
> Apenas os serviços da Azure podem publicar eventos para tópicos do sistema. Portanto, você não obtém um ponto final ou chaves de acesso que você pode usar para publicar eventos como você faz para tópicos ou domínios personalizados.

## <a name="azure-services-that-support-system-topics"></a>Serviços Azure que suportam tópicos do sistema
Aqui está a lista atual de serviços Azure que suportam a criação de tópicos do sistema sobre eles.

- [Configuração da Aplicação Azure](event-schema-app-configuration.md)
- [Serviço de Aplicações do Azure](event-schema-app-service.md)
- [Armazenamento de Blobs do Azure](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Hub IoT do Azure](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Serviços de Multimédia do Azure](event-schema-media-services.md)
- [Grupos de recursos do Azure](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Subscrições do Azure](event-schema-subscriptions.md)
- [Cache do Azure para Redis](event-schema-azure-cache.md)

## <a name="system-topics-as-azure-resources"></a>Tópicos do sistema como recursos Azure
No passado, um tópico do sistema estava implícito e não estava exposto à simplicidade. Os tópicos do sistema são agora visíveis como recursos Azure e fornecem as seguintes capacidades:

- [Ver tópicos do sistema no portal Azure](create-view-manage-system-topics.md#view-all-system-topics)
- Modelos de Gestor de Recursos de Exportação para tópicos do sistema e subscrições de eventos no portal Azure
- [Configurar registos de diagnóstico para tópicos do sistema](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- Configurar alertas sobre falhas de publicação e entrega 

## <a name="lifecycle-of-system-topics"></a>Ciclo de vida dos tópicos do sistema
Pode criar um tópico de sistema de duas formas: 

- Crie uma [subscrição de eventos num recurso Azure como recurso de extensão,](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)que cria automaticamente um tópico de sistema com o nome no formato: `<Azure resource name>-<GUID>` . O tópico do sistema criado desta forma é automaticamente eliminado quando a última subscrição do evento para o tópico é eliminada. 
- Crie um tópico de sistema para um recurso Azure e, em seguida, crie uma subscrição de eventos para esse tópico do sistema. Quando utilizar este método, pode especificar um nome para o tópico do sistema. O tópico do sistema não é eliminado automaticamente quando a última subscrição do evento é eliminada. Tem de apagar manualmente. 

    Quando utilizar o portal Azure, está sempre a utilizar este método. Quando cria uma subscrição de eventos utilizando a página [ **Eventos** de um recurso Azure,](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)o tópico do sistema é criado primeiro e, em seguida, a subscrição para o tópico é criada. Primeiro, pode criar um tópico de sistema utilizando a página [ **Tópicos do Sistema de Grelha de Eventos**](create-view-manage-system-topics.md#create-a-system-topic) e, em seguida, criar uma subscrição para esse tópico. 

Quando utilizar o modelo [CLI,](create-view-manage-system-topics-cli.md) [REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)ou [Azure Resource Manager,](create-view-manage-system-topics-arm.md)pode escolher qualquer um dos métodos acima referidos. Recomendamos que crie primeiro um tópico de sistema e depois crie uma subscrição sobre o tema, uma vez que esta é a mais recente forma de criar tópicos do sistema.

A criação de tópicos do sistema falha se tiver configurado políticas Azure de tal forma que o serviço de Grade de Eventos não possa criá-lo. Por exemplo, pode ter uma política que permite a criação de apenas certos tipos de recursos (por exemplo: Azure Storage, Azure Event Hubs, etc.) na subscrição. 

## <a name="location-and-resource-group-for-a-system-topic"></a>Localização e grupo de recursos para um tópico de sistema
Para fontes de eventos Azure que se encontram numa região/localização específica, o tópico do sistema é criado no mesmo local que a fonte do evento Azure. Por exemplo, se criar uma subscrição de eventos para um armazenamento de blob Azure no Leste dos EUA, o tópico do sistema é criado no Leste dos EUA. Para fontes globais de eventos Azure, como subscrições Azure, grupos de recursos ou Azure Maps, a Grade de Eventos cria o tópico do sistema na localização **global.** 

Em geral, o tópico do sistema é criado no mesmo grupo de recursos em que a fonte de eventos Azure está. Para subscrições de eventos criadas no âmbito de subscrição do Azure, o tópico do sistema é criado no grupo de recursos **Default-EventGrid** na região **oeste dos EUA 2.** Se o grupo de recursos não existir, a Azure Event Grid cria-o antes de criar o tópico do sistema. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Criar, visualizar e gerir tópicos do sistema utilizando o portal Azure](create-view-manage-system-topics.md).
- [Criar, ver e gerir tópicos do sistema de grade de eventos utilizando o Azure CLI](create-view-manage-system-topics-cli.md)
- [Crie tópicos de sistema de grelha de eventos utilizando modelos de Gestor de Recursos Azure](create-view-manage-system-topics-arm.md)
