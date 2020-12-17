---
title: Tarefas e aplicações de replicação de mensagens - Azure Service Bus / Microsoft Docs
description: Este artigo fornece uma visão geral das tarefas e aplicações de replicação de mensagens de construção com Funções Azure
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657496"
---
# <a name="message-replication-tasks-and-applications"></a>Tarefas e aplicações de replicação de mensagens

Como explicado no artigo da federação de replicação de [mensagens e inter-regiões,](service-bus-federation-overview.md) a replicação de sequências de mensagens entre pares de entidades de Service Bus e entre Service Bus e outras fontes de mensagens e alvos geralmente se apoia em Funções Azure.

[A azure Functions](../azure-functions/functions-overview.md) é um ambiente de execução escalável e fiável para configurar e executar aplicações sem servidor, incluindo [replicação de mensagens e](service-bus-federation-overview.md) tarefas da federação.

Nesta visão geral, você vai aprender sobre as capacidades incorporadas da Azure Functions para tais aplicações, sobre blocos de código que você pode adaptar e modificar para tarefas de transformação, e sobre como configurar uma aplicação Azure Functions de modo que se integra idealmente com Service Bus e outros serviços de Mensagens Azure. Para muitos detalhes, este artigo irá apontar para a documentação do Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
