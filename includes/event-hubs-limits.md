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
ms.openlocfilehash: c163e3cce862640d43f8696dca4eeef29f2ae12a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912463"
---
A tabela a seguir lista cotas e limites específicos para os [hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para obter informações sobre os preços dos hubs de eventos, consulte [preços dos hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Scope | Notas | Value |
| --- | --- | --- | --- |
| Número de namespaces de hubs de eventos por assinatura |Subscription |- |100 |
| Número de hubs de eventos por namespace |Espaço de Nomes |As solicitações subsequentes para a criação de um novo hub de eventos são rejeitadas. |10 |
| Número de partições por Hub de eventos |Entidade |- |32 |
| Tamanho máximo do evento de hubs de eventos|Entidade |- |1 MB |
| Tamanho máximo de um nome de Hub de eventos |Entidade |- |50 caracteres |
| Número de receptores de não época por grupo de consumidores |Entidade |- |5 |
| Unidades de débito máximas |Espaço de Nomes |Exceder o limite de unidade de produtividade faz com que os dados sejam restringidos e gera uma [exceção de servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um número maior de unidades de produtividade para uma camada Standard, arquivo uma [solicitação de suporte](/azure/azure-supportability/how-to-create-azure-support-request). [Unidades de produtividade adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20 em uma base de compra confirmada. |20 |
| Número de regras de autorização por namespace |Espaço de Nomes|As solicitações subsequentes para a criação da regra de autorização são rejeitadas.|12 |
| Número de chamadas para o método GetRuntimeInformation | Entidade | - | 50 por segundo | 
| Número de regras de rede virtual (VNet) e de configuração de IP | Entidade | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Hubs de eventos básico e padrão-cotas e limites
| Limite | Scope | Notas | Básica | Standard |
| --- | --- | --- | -- | --- |
| Número de grupos de consumidores por Hub de eventos |Entidade | - |1 |20 |
| Número de conexões AMQP por namespace |Espaço de Nomes |As solicitações subsequentes para conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |100 |5,000|
| Período de retenção máximo de dados de evento |Entidade | - |1 dia |1-7 dias |
|Apache Kafka namespace habilitado|Espaço de Nomes |Namespace de hubs de eventos transmite aplicativos usando o protocolo Kafka |Não | Sim |
|Captura |Entidade | Quando habilitado, micro lotes no mesmo fluxo |Não |Sim |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Hubs de Eventos Dedicados-cotas e limites
A oferta de Hubs de Eventos Dedicados é cobrada a um preço mensal fixo, com um mínimo de 4 horas de uso. A camada dedicada oferece todos os recursos do plano padrão, mas com capacidade e limites de escala empresarial para clientes com cargas de trabalho exigentes. 

| Funcionalidade | Limites |
| --- | ---|
| Largura de banda |  20 CUs |
| Espaços de nomes | 50 por CU |
| Hubs de Eventos |  1000 por namespace |
| Eventos de entrada | Incluída |
| Tamanho da Mensagem | 1 milhão bytes |
| Partições | 2000 por CU |
| Grupos de consumidores | Nenhum limite por CU, 1000 por Hub de eventos |
| Ligações mediadas | 100 mil incluídos |
| Retenção de Mensagens | Até 7 dias (retenção de 90 dias em breve), 10 TB incluídos por CU |
| Captura | Incluída |
