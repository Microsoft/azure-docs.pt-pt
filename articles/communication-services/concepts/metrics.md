---
title: Definições métricas para o Serviço de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Este documento abrange definições de métricas disponíveis no portal Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 05/19/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0a80e289a6fbb8dfe9b725eaa1ef9e114d2896fd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691387"
---
# <a name="metrics-overview"></a>Métricas panorâmicas

A Azure Communication Services fornece atualmente métricas para Chat e SMS. [O Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md) pode ser usado para traçar os seus próprios gráficos, investigar anomalias nos seus valores métricos e compreender o tráfego da API utilizando os dados métricos que o Chat e o SMS solicitam emitir.

## <a name="where-to-find-metrics"></a>Onde encontrar métricas

Os serviços de chat e SMS nos Serviços de Comunicação Azure emitem métricas para pedidos de API. Estas métricas podem ser encontradas na lâmina métrica sob o seu recurso serviços de comunicação. Também pode criar painéis permanentes utilizando a lâmina dos livros de trabalho sob o seu recurso Serviços de Comunicação.

## <a name="metric-definitions"></a>Definições métricas

Existem dois tipos de pedidos que estão representados nas métricas dos Serviços de Comunicação: **pedidos de Chat API** e **pedidos de API por SMS**.

As métricas de pedido de Chat e SMS API contêm três dimensões que pode utilizar para filtrar os seus dados métricos. Estas dimensões podem ser agregadas em conjunto utilizando o `Count` tipo de agregação e suportam todas as séries de tempo padrão de agregação Azure, incluindo, `Sum` e `Average` `Min` `Max` .

Mais informações sobre tipos de agregação suportados e agregações de séries de tempo podem ser encontradas [Características avançadas do Azure Metrics Explorer](../../azure-monitor/essentials/metrics-charts.md#aggregation)

- **Operação** - Todas as operações ou rotas que possam ser chamadas no gateway ACS Chat.
- **Código de Estado** - A resposta do código de estado enviada após o pedido.
- **StatusSubClass** - A série de códigos de estado enviada após a resposta. 


### <a name="chat-api-request-metric-operations"></a>Chat API solicitar operações métricas

As seguintes operações estão disponíveis nas métricas de pedido da Chat API:

| Operação / Rota    | Descrição                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | Recebe uma mensagem por identificação de mensagem. |
| ListChatMessages     | Recebe uma lista de mensagens de chat de um fio. |
| SendChatMessage      | Envia uma mensagem de chat para um fio. |
| UpdateChatMessage    | Atualiza uma mensagem de chat. |
| DeleteChatMessage    | Elimina uma mensagem de chat. |
| GetChatThread        | Tem um fio de conversa. |
| ListChatThreads      | Obtém a lista de fios de chat de um utilizador. |
| UpdateChatThread     | Atualiza as propriedades de um fio de chat. |
| CriarChatThread     | Cria um fio de chat. |
| DeleteChatThread     | Apaga um fio. |
| GetReadReceipts      | Recebe recibos de leitura por um fio. |
| SendReadReceipt      | Envia um evento de recibo de leitura para um fio, em nome de um utilizador. |
| SendTypingIndicator           | Publica um evento de dactilografia a um fio, em nome de um utilizador. |
| ListChatThreadParticipants    | Apanha os membros de um fio. |
| AddChatThreadParticipants     | Adiciona os membros do fio a um fio. Se os membros já existirem, não ocorre qualquer alteração. |
| RemoverChatThreadParticipant   | Retire um membro de um fio. |

:::image type="content" source="./media/chat-metric.png" alt-text="Chat API Request Metric.":::

Se for feito um pedido a uma operação que não seja reconhecida, receberá uma resposta de valor "Má Rota".

### <a name="sms-api-requests"></a>Pedidos de API por SMS

As seguintes operações estão disponíveis nas métricas de pedido de SMS API:

| Operação / Rota    | Descrição                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | Envia uma mensagem SMS. |
| SMSDeliveryReportsReceived     | Obtém relatórios de entrega de SMS |
| SMSMessagesReceived      | Recebe mensagens SMS. |


:::image type="content" source="./media/sms-metric.png" alt-text="SMS API Request Metric.":::

### <a name="authentication-api-requests"></a>Pedidos de autenticação da API

As seguintes operações estão disponíveis nas métricas de pedido de autenticação da API:

| Operação / Rota    | Descrição                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | Cria uma identidade que representa um único utilizador. |
| DeleteIdentity       | Elimina uma identidade. |
| CriarToken          | Cria um símbolo de acesso. |
| RevogarToken          | Revoga todos os tokens de acesso criados para uma identidade antes de um tempo dado. |

:::image type="content" source="./media/acs-auth-metrics.png" alt-text="Métrica de pedido de autenticação.":::

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [as Métricas da Plataforma de Dados](../../azure-monitor/essentials/data-platform-metrics.md)
