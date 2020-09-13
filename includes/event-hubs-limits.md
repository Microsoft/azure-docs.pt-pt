---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8700bbfe697a6b5fb81380831950d704fcb1f5ff
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90012793"
---
As tabelas a seguir fornecem quotas e limites específicos aos [Hubs de Eventos Azure](https://azure.microsoft.com/services/event-hubs/). Para obter informações sobre os preços dos Event Hubs, consulte [os preços do Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Os seguintes limites são comuns em níveis básicos e padrão. 

| Limite | Âmbito | Notas | Valor |
| --- | --- | --- | --- |
| Número de espaços de nomes de Centros de Eventos por subscrição |Subscrição |- |100 |
| Número de centros de eventos por espaço de nome |Espaço de Nomes |Os pedidos subsequentes para a criação de um novo centro de eventos são rejeitados. |10 |
| Número de divisórias por centro de eventos |Entidade |- |32 |
| Tamanho máximo de um nome de hub de evento |Entidade |- | 256 caracteres |
| Tamanho máximo de um nome de grupo de consumidores |Entidade |- | 256 caracteres |
| Número de recetores não-época por grupo de consumidores |Entidade |- |5 |
| Unidades de produção máxima |Espaço de Nomes |Exceder o limite de unidade de produção faz com que os seus dados sejam estrangulados e gera uma [exceção ocupada do servidor](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um maior número de unidades de produção para um nível Standard, apresente um pedido de [suporte](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Unidades de produção adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20 numa base de compra comprometida. |20 |
| Número de regras de autorização por espaço de nome |Espaço de Nomes|Os pedidos subsequentes de criação de regras de autorização são rejeitados.|12 |
| Número de chamadas para o método GetRuntimeInformation | Entidade | - | 50 por segundo | 
| Número de regras de rede virtual (VNet) e IP Config | Entidade | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Centros de Eventos Básicos e Standard - quotas e limites
| Limite | Âmbito | Notas | Básica | Standard |
| --- | --- | --- | -- | --- |
| Tamanho máximo do evento Event Hubs|Entidade | &nbsp; | 256 KB | 1 MB |
| Número de grupos de consumidores por centro de eventos |Entidade | &nbsp; |1 |20 |
| Número de ligações AMQP por espaço de nome |Espaço de Nomes |Os pedidos subsequentes de ligações adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |100 |5000|
| Período máximo de retenção dos dados do evento |Entidade | &nbsp; |1 dia |1-7 dias |
|Apache Kafka viabilizar espaço de nome|Espaço de Nomes |Aplicações de espaço de nome do Event Hubs usando o protocolo Kafka. Para mais informações, consulte [Use Azure Event Hubs a partir de aplicações Apache Kafka](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md). |No | Yes |
|Recolha |Entidade | Quando ativados, micro-lotes no mesmo fluxo. Para mais informações, consulte [eventos de captura através de Azure Event Hubs no Azure Blob Storage ou no Azure Data Lake Storage](../articles/event-hubs/event-hubs-capture-overview.md). |No |Yes |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Centros de Eventos Dedicados - quotas e limites
A oferta dedicada do Event Hubs é faturada a um preço mensal fixo, com um mínimo de 4 horas de utilização. O tier dedicado oferece todas as características do plano Standard, mas com capacidade de escala empresarial e limites para clientes com cargas de trabalho exigentes. 

| Funcionalidade | Limites |
| --- | ---|
| Largura de banda |  20 CUs |
| Espaços de nomes | 50 por CU |
| Hubs de Eventos |  1000 por espaço de nome |
| Eventos ingressos | Incluída |
| Tamanho da mensagem | 1 MB |
| Partições | 2000 por CU |
| Grupos de consumidores | Sem limite por CU, 1000 por centro de eventos |
| Conexões intermediadas | 100 K incluído |
| Retenção de mensagens | 90 dias, 10 TB incluídos por CU |
| Recolha | Incluída |
