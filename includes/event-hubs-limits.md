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
ms.openlocfilehash: 320fa542f2b786f0a256c22f2d2eb299c476dcae
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94362760"
---
As tabelas a seguir fornecem quotas e limites específicos aos [Hubs de Eventos Azure](https://azure.microsoft.com/services/event-hubs/). Para obter informações sobre os preços dos Event Hubs, consulte [os preços do Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="common-limits-for-all-tiers"></a>Limites comuns para todos os níveis
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


## <a name="basic-vs-standard-tiers"></a>Níveis básicos vs. padrão
O quadro a seguir mostra limites que podem ser diferentes para os níveis básicos e padrão. 

| Limite | Notas | Básica | Standard |
|---|---|--|---|
| Tamanho máximo do evento Event Hubs| &nbsp; | 256 KB | 1 MB |
| Número de grupos de consumidores por centro de eventos | &nbsp; |1 |20 |
| Número de ligações AMQP por espaço de nome | Os pedidos subsequentes de ligações adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |100 |5000|
| Período máximo de retenção dos dados do evento | &nbsp; |1 dia |1-7 dias |
| Unidades de produção máxima |Ultrapassar este limite faz com que os seus dados sejam estrangulados e gera uma [exceção ocupada pelo servidor](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um maior número de unidades de produção para um nível Standard, apresente um pedido de [suporte](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Unidades de produção adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20 numa base de compra comprometida. |20 | 20 | 

## <a name="dedicated-tier-vs-standard-tier"></a>Nível dedicado vs. nível padrão
A oferta dedicada do Event Hubs é faturada a um preço mensal fixo, com um mínimo de 4 horas de utilização. O tier dedicado oferece todas as características do plano Standard, mas com capacidade de escala empresarial e limites para clientes com cargas de trabalho exigentes. 

Consulte este [documento](https://docs.microsoft.com/azure/event-hubs/event-hubs-dedicated-cluster-create-portal) sobre como criar um cluster dedicado de Centros de Eventos utilizando o portal Azure.

| Funcionalidade | Standard | Dedicada |
| --- |:---|:---|
| Largura de banda | 20 TUs (até 40 TUs) | 20 CUs |
| Espaços de nomes |  1 | 50 por CU |
| Hubs de Eventos |  10 por espaço de nome | 1000 por espaço de nome |
| Eventos ingressos | Pagar por milhão de eventos | Incluídos |
| Tamanho da mensagem | 1 Milhão de Bytes | 1 Milhão de Bytes |
| Partições | 32 por Centro de Eventos | 1024 por centro de eventos<br/>2000 por CU |
| Grupos de consumidores | 20 por Centro de Eventos | Sem limite por CU, 1000 por centro de eventos |
| Conexões intermediadas | 1.000 incluídos, 5.000 no máximo | 100 K incluído e máx |
| Retenção de mensagens | 7 dias, 84 GB incluídos por TU | 90 dias, 10 TB incluídos por CU |
| Recolha | Pagamento por hora | Incluídos |


## <a name="schema-registry-limitations"></a>Limitações do registo de esquemas

### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Limites que são os mesmos para níveis **standard** e **dedicados** 
| Funcionalidade | Limite | 
|---|---|--|
| Comprimento máximo de um nome de grupo de esquema | 50 |  
| Comprimento máximo de um nome de esquema | 100 |    
| Tamanho em bytes por esquema | 1 MB |   
| Número de propriedades por grupo schema | 1024 |
| Tamanho em bytes por chave de propriedade de grupo | 256 | 
| Tamanho em bytes por valor de propriedade de grupo | 1024 | 


### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Limites diferentes para níveis **standard** e **dedicados** 

| Limite | Standard | Dedicada | 
|---|---|--|---|
| Tamanho do registo de esquemas (namespace) em mega bytes | 25 |  1024 |
| Número de grupos de esquemas num registo de esquemas ou espaço de nome | 1 - excluindo o grupo predefinido | 1000 |
| Número de versões de esquema em todos os grupos de esquemas | 25 | 10000 |





