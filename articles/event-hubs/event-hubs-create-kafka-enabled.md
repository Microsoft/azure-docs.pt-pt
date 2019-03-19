---
title: Criar um hub de eventos ativado do Apache Kafka - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece um passo a passo para criar um Apache Kafka ativado o espaço de nomes de Hubs de eventos do Azure com o portal do Azure.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 125da95349fce0e75b44b5619baba28d34a74be1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000503"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Criar os hubs de eventos do Apache Kafka ativada

Os Hubs de eventos do Azure é uma transmissão em fluxo de plataforma como serviço (PaaS) que ingere milhões de eventos por segundo e oferece baixa latência e alto débito para análise em tempo real e visualização de grandes volumes de dados.

Os Hubs de eventos do Azure fornece um ponto de extremidade do Kafka. Este ponto final permite que o espaço de nomes de Hubs de eventos compreender de forma nativa [Apache Kafka](https://kafka.apache.org/intro) protocolo e APIs de mensagens. Com esta capacidade, pode comunicar com os hubs de eventos, tal como faria com tópicos do Kafka sem alterar os seus clientes de protocolo ou os seus próprios clusters em execução. Os Hubs de eventos suportam [versões do Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) e mais tarde.

Este artigo descreve como criar um espaço de nomes de Hubs de eventos e obter a cadeia de ligação necessária para ligar aplicações de Kafka com os hubs de eventos de Kafka-ativado.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos ativado para Kafka

1. Inicie sessão para o [portal do Azure][Azure portal]e clique em **criar um recurso** na parte superior esquerda do ecrã.

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

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os Hubs de eventos, visite estas ligações:

* [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Saiba mais sobre Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
