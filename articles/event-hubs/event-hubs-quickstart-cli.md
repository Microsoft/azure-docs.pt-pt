---
title: Criar um hub de eventos utilizando o Azure CLI - Azure Event Hubs [ Hubs de Eventos Azure] Microsoft Docs
description: Este início rápido descreve como criar um hub de eventos com a CLI do Azure e, em seguida, enviar e receber eventos com Java.
services: event-hubs
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: java
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 92fd7d15ee5bc54cc41b78f4ba0d078d3f8fac6b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77162638"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>Início Rápido: Criar um hub de eventos com a CLI do Azure

Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Neste início rápido, vai criar um hub de eventos com a CLI Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, precisa de uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita][] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI do Azure localmente, este tutorial requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Execute `az --version` para verificar a sua versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Os passos seguintes não são necessários se estiver a executar comandos no Cloud Shell. Se estiver a executar a CLI localmente, siga os seguintes passos para iniciar sessão no Azure e definir a sua subscrição atual:

Execute o seguinte comando para iniciar sessão no Azure:

```azurecli-interactive
az login
```

Defina o contexto da subscrição atual. Substitua `MyAzureSub` pelo nome da subscrição do Azure que quer utilizar:

```azurecli-interactive
az account set --subscription MyAzureSub
``` 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é uma coleção lógica de recursos do Azure. Todos os recursos são implementados e geridos num grupo de recursos. Execute o seguinte comando para criar um grupo de recursos:

```azurecli-interactive
# Create a resource group. Specify a name for the resource group.
az group create --name <resource group name> --location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos
Um espaço de nomes dos Hubs de Eventos fornece um contentor de âmbito exclusivo, referenciado pelo respetivo nome de domínio completamente qualificado, no qual cria um ou mais hubs de eventos. Para criar um espaço de nomes no seu grupo de recursos, execute o seguinte comando:

```azurecli-interactive
# Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
```

## <a name="create-an-event-hub"></a>Criar um hub de eventos
Execute o seguinte comando para criar um hub de eventos:

```azurecli-interactive
# Create an event hub. Specify a name for the event hub. 
az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
```

Parabéns! Utilizou a CLI do Azure para criar um espaço de nomes dos Hubs de Eventos e um hub de eventos nesse espaço de nomes. 

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um grupo de recursos, um espaço de nomes de Hubs de Eventos e um hub de eventos. Para instruções passo a passo para enviar eventos para (ou) receber eventos de um centro de eventos, consulte o **Envio e receba** tutoriais de eventos: 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Ir](event-hubs-go-get-started-send.md)
- [C (apenas enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (apenas receber)](event-hubs-storm-getstarted-receive.md)

[crie uma conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
