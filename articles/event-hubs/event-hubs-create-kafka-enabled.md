---
title: Criar Apache Kafka Hub de eventos habilitado-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece instruções para criar um Apache Kafka namespace de hubs de eventos do Azure habilitado usando o portal do Azure.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 9ce0f74ec6d4e536bfb3fe827ae6f8ae143b640e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555811"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Criar Apache Kafka hubs de eventos habilitados

Os hubs de eventos do Azure são uma plataforma de streaming de Big data como um serviço (PaaS) que ingere milhões de eventos por segundo e fornece baixa latência e alta taxa de transferência para análise e visualização em tempo real.

Os hubs de eventos do Azure fornecem um ponto de extremidade Kafka. Esse ponto de extremidade permite que seu namespace de hubs de eventos entenda nativamente [Apache Kafka](https://kafka.apache.org/intro) protocolo de mensagem e APIs. Com esse recurso, você pode se comunicar com seus hubs de eventos como faria com os tópicos do Kafka sem alterar seus clientes de protocolo nem executar seus próprios clusters. Os hubs de eventos oferecem suporte às [versões 1,0](https://kafka.apache.org/10/documentation.html) e posteriores do Apache Kafka.

Este artigo descreve como criar um namespace de hubs de eventos e obter a cadeia de conexão necessária para conectar aplicativos Kafka a hubs de eventos habilitados para Kafka.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos ativado para Kafka

1. Entre no [portal do Azure][Azure portal]e clique em **criar um recurso** na parte superior esquerda da tela.

2. Procure por Hubs de Eventos e selecione as opções mostradas aqui:
    
    ![Procurar por Hubs de Eventos no portal](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Indique um nome exclusivo e ative o Kafka no espaço de nomes. Clique em **Criar**.
    
    ![Criar um espaço de nomes](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Quando o espaço de nomes for criado, no separador **Definições**, clique em **Políticas de acesso partilhado** para obter a cadeia de ligação.

    ![Clique em Políticas de acesso partilhado](./media/event-hubs-create/create-event-hub7.png)

5. Pode escolher **RootManageSharedAccessKey** predefinida ou adicionar uma política nova. Clique no nome da política e copie a cadeia de ligação. 
    
    ![Selecionar uma política](./media/event-hubs-create/create-event-hub8.png)
 
6. Adicione essa cadeia de ligação à sua configuração do Kafka.

Agora, já pode transmitir em fluxo eventos das suas aplicações que utilizam o protocolo Kafka para os Hubs de eventos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os hubs de eventos, visite estes links:

* [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Saiba mais sobre Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
