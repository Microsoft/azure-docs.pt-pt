---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d0015868f6b53c2903b83795839e2dbcffd09ce4
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116763"
---
As tabelas a seguir fornecem quotas e limites específicos aos [Hubs de Eventos Azure](https://azure.microsoft.com/services/event-hubs/). Para obter informações sobre os preços dos Event Hubs, consulte [os preços do Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Limites comuns para todos os níveis
Os seguintes limites são comuns em todos os níveis. 

| Limite |  Notas | Valor |
| --- |  --- | --- |
| Número de espaços de nomes de Centros de Eventos por subscrição |- |100 |
| Número de centros de eventos por espaço de nome | Os pedidos subsequentes para a criação de um novo centro de eventos são rejeitados. |10 |
| Número de divisórias por centro de eventos |- |32 |
| Tamanho de um nome de centro de evento |- | 256 caracteres |
| Tamanho de um nome de grupo de consumidores |- | 256 caracteres |
| Número de recetores não-época por grupo de consumidores |- |5 |
| Número de regras de autorização por espaço de nome | Os pedidos subsequentes de criação de regras de autorização são rejeitados.|12 |
| Número de chamadas para o método GetRuntimeInformation |  - | 50 por segundo | 
| Número de regras de rede virtual (VNet) e IP Config | - | 128 | 


### <a name="basic-and-standard-tiers"></a>Níveis básicos e padrão
O quadro a seguir mostra limites que podem ser diferentes para os níveis básicos e padrão. 

| Limite | Notas | Básica | Standard |
| --- |  --- | -- | --- |
| Tamanho máximo do evento Event Hubs| &nbsp; | 256 KB | 1 MB |
| Número de grupos de consumidores por centro de eventos | &nbsp; |1 |20 |
| Número de ligações AMQP por espaço de nome | Os pedidos subsequentes de ligações adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |100 |5000|
| Período máximo de retenção dos dados do evento | &nbsp; |1 dia |1-7 dias |
| Unidades de produção máxima |Exceder o limite de unidade de produção faz com que os seus dados sejam estrangulados e gera uma [exceção ocupada do servidor](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um maior número de unidades de produção para um nível Standard, apresente um pedido de [suporte](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Unidades de produção adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20 numa base de compra comprometida. |20 | 20 | 

### <a name="dedicated-tier"></a>Nível dedicado
A oferta dedicada do Event Hubs é faturada a um preço mensal fixo, com um mínimo de 4 horas de utilização. O tier dedicado oferece todas as características do plano Standard, mas com capacidade de escala empresarial e limites para clientes com cargas de trabalho exigentes. 

| Funcionalidade | Limites |
| --- | ---|
| Largura de banda |  20 CUs |
| Espaços de nomes | 50 por CU |
| Hubs de Eventos |  1000 por espaço de nome |
| Tamanho da mensagem | 1 MB |
| Partições | 2000 por CU |
| Grupos de consumidores | Sem limite por CU, 1000 por centro de eventos |
| Conexões intermediadas | 100 K incluído |
| Tempo de retenção de mensagens | 90 dias, 10 TB incluídos por CU |
| Eventos ingressos | Incluídos |
| Recolha | Incluídos |


### <a name="schema-registry-limitations"></a>Limitações do registo de esquemas

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Limites que são os mesmos para níveis **standard** e **dedicados** 
| Funcionalidade | Limite | 
| --- |  --- | -- |
| Comprimento máximo de um nome de grupo de esquema | 50 |  
| Comprimento máximo de um nome de esquema | 100 |    
| Tamanho em bytes por esquema | 1 MB |   
| Número de propriedades por grupo schema | 1024 |
| Tamanho em bytes por chave de propriedade de grupo | 256 | 
| Tamanho em bytes por valor de propriedade de grupo | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Limites diferentes para níveis **standard** e **dedicados** 

| Limite | Standard | Dedicada | 
| --- |  --- | -- | --- |
| Tamanho do registo de esquemas (namespace) em mega bytes | 25 |  1024 |
| Número de grupos de esquemas num registo de esquemas (espaço de nome)| 1 (excluindo o padrão) | 1000 |
| Número de versões de esquema em todos os grupos de esquemas | 25 | 10000 |





