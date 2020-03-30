---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75902002"
---
As tabelas seguintes fornecem quotas e limites específicos aos Hubs de [Eventos Azure.](https://azure.microsoft.com/services/event-hubs/) Para obter informações sobre os preços dos Hubs de Eventos, consulte os preços dos Centros de [Eventos.](https://azure.microsoft.com/pricing/details/event-hubs/)

Os seguintes limites são comuns em níveis básicos, standard e dedicados. 

| Limite | Âmbito | Notas | Valor |
| --- | --- | --- | --- |
| Número de espaços de nome sinos de eventos por subscrição |Subscrição |- |100 |
| Número de centros de eventos por espaço de nome |Espaço de nomes |Os pedidos subsequentes de criação de um novo centro de eventos são rejeitados. |10 |
| Número de divisórias por centro de eventos |Entidade |- |32 |
| Tamanho máximo de um nome de centro de eventos |Entidade |- |50 caracteres |
| Número de recetores não época por grupo de consumidores |Entidade |- |5 |
| Unidades de entrada máximas |Espaço de nomes |Exceder o limite da unidade de entrada faz com que os seus dados sejam estrangulados e gera uma [exceção ocupada pelo servidor](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um maior número de unidades de entrada para um nível Standard, preencha um pedido de [suporte](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Unidades](../articles/event-hubs/event-hubs-auto-inflate.md) de entrada adicionais estão disponíveis em blocos de 20 numa base de compra comprometida. |20 |
| Número de regras de autorização por espaço de nome |Espaço de nomes|Os pedidos subsequentes de criação de regras de autorização são rejeitados.|12 |
| Número de chamadas para o método GetRuntimeInformation | Entidade | - | 50 por segundo | 
| Número de regras de rede virtual (VNet) e IP Config | Entidade | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Centros de Eventos Básicos e Standard - quotas e limites
| Limite | Âmbito | Notas | Básico | Standard |
| --- | --- | --- | -- | --- |
| Tamanho máximo do evento Event Hubs|Entidade | &nbsp; | 256 KB | 1 MB |
| Número de grupos de consumidores por centro de eventos |Entidade | &nbsp; |1 |20 |
| Número de ligações AMQP por espaço de nome |Espaço de nomes |Os pedidos subsequentes de ligações adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |100 |5000|
| Período máximo de retenção dos dados do evento |Entidade | &nbsp; |1 dia |1-7 dias |
|Apache Kafka habilitado espaço de nome|Espaço de nomes |Aplicações de fluxos de espaço de espaço de eventos Hubs usando o protocolo de Kafka |Não | Sim |
|Captura |Entidade | Quando ativado, microlotes no mesmo fluxo |Não |Sim |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Hubs de eventodedicados - quotas e limites
A oferta dedicada do Event Hubs é faturada a um preço mensal fixo, com um mínimo de 4 horas de utilização. O nível Dedicado oferece todas as funcionalidades do plano Standard, mas com capacidade de escala empresarial e limites para clientes com cargas de trabalho exigentes. 

| Funcionalidade | Limites |
| --- | ---|
| Largura de banda |  20 CUs |
| Espaços de nomes | 50 por CU |
| Event Hubs |  1000 por espaço de nome |
| Eventos de ingresso | Incluído |
| Tamanho da mensagem | 1 MB |
| Partições | 2000 por CU |
| Grupos de consumidores | Sem limite por CU, 1000 por centro de eventos |
| Ligações intermediadas | 100 K incluídos |
| Retenção de Mensagens | 90 dias, 10 TB incluídos por CU |
| Captura | Incluído |
