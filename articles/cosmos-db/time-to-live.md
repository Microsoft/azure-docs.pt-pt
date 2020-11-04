---
title: Expire dados em Azure Cosmos DB com tempo para viver
description: Com a TTL, a Microsoft Azure Cosmos DB fornece a capacidade de ter documentos automaticamente purgados do sistema após um período de tempo.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/02/2020
ms.reviewer: sngun
ms.openlocfilehash: f439fcd8b2aa1c75e1aff2c6b775921beabbcddf
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340561"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Time to Live [TTL] no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Com **o Time to Live** ou TTL, o Azure Cosmos DB fornece a capacidade de eliminar automaticamente os itens de um recipiente após um determinado período de tempo. Por predefinição, pode definir a hora para viver ao nível do recipiente e sobrepor o valor numa base por item. Depois de definir o TTL num recipiente ou a um nível de item, a Azure Cosmos DB removerá automaticamente estes itens após o período de tempo, desde a última modificação. O tempo de vida do valor é configurado em segundos. Quando configurar a TTL, o sistema eliminará automaticamente os itens caducados com base no valor TTL, sem necessidade de uma operação de eliminação que seja explicitamente emitida pela aplicação do cliente. O valor máximo para TTL é 2147483647.

A eliminação de itens caducados é uma tarefa de fundo que consome [Unidades de Pedido](request-units.md)sobrantes, ou seja, Unidades de Pedido que não tenham sido consumidas por solicitações do utilizador. Mesmo depois de expirado o TTL, se o contentor estiver sobrecarregado com pedidos e se não houver RU suficiente disponível, a eliminação de dados é retardada. Os dados são eliminados uma vez que existam RUs suficientes disponíveis para executar a operação de eliminação. Embora a eliminação de dados seja adiada, os dados não são devolvidos por quaisquer consultas (por qualquer API) depois de expirado o TTL.

> Este conteúdo está relacionado com a loja de transações Azure Cosmos DB TTL. Se procura uma loja analidade TTL, que permite cenários noETL HTAP através do [Azure Synapse Link,](./synapse-link.md)clique [aqui.](./analytical-store-introduction.md#analytical-ttl)

## <a name="time-to-live-for-containers-and-items"></a>Tempo de viver para contentores e itens

O valor do tempo de vida é definido em segundos, e é interpretado como um delta a partir do momento em que um item foi modificado pela última vez. Pode definir a hora para viver num recipiente ou num item dentro do recipiente:

1. **Tempo para viver num recipiente** (conjunto de utilização): `DefaultTimeToLive`

   - Se faltar (ou definido para nulo), os itens não expiram automaticamente.

   - Se presente e o valor for definido para "-1", é igual ao infinito, e os itens não expiram por predefinição.

   - Se o presente e o valor estiver definido para algum número *"n"* – os itens expirarão *"n"* segundos após o seu último tempo modificado.

2. **Tempo para viver num item** (conjunto de utilização): `ttl`

   - Este Imóvel só é aplicável se `DefaultTimeToLive` estiver presente e não for definido como nulo para o contentor principal.

   - Se estiver presente, sobrepõe-se ao `DefaultTimeToLive` valor do recipiente-mãe.

## <a name="time-to-live-configurations"></a>Configurações tempo para viver

* Se o TTL estiver definido para *"n"* num recipiente, os artigos desse recipiente expirarão após *n* segundos.  Se houver itens no mesmo recipiente que tenham tempo próprio para viver, definidos para -1 (indicando que não expiram) ou se alguns itens ultrapassaram o tempo de vida com um número diferente, estes itens expiram com base no seu próprio valor TTL configurado. 

* Se a TTL não estiver colocada num recipiente, então a hora de viver num item neste recipiente não tem efeito. 

* Se a TTL num recipiente estiver definida para -1, um item neste recipiente que tenha tempo de vida definido para n, expirará após n segundos, e os restantes itens não expirarão.

## <a name="examples"></a>Exemplos

Esta secção mostra alguns exemplos com diferentes tempos para valores vivos atribuídos a contentores e itens:

### <a name="example-1"></a>Exemplo 1

TTL no recipiente é definido para nulo (DefaultTimeToLive = nulo)

|TTL no item| Resultado|
|---|---|
|ttl = nulo|    A TTL está desativada. O item nunca expirará (predefinição).|
|ttl = -1   |A TTL está desativada. O artigo nunca expirará.|
|ttl = 2000 |A TTL está desativada. O artigo nunca expirará.|


### <a name="example-2"></a>Exemplo 2

TTL no recipiente é definido para -1 (DefaultTimeToLive = -1)

|TTL no item| Resultado|
|---|---|
|ttl = nulo |O TTL está ativado. O item nunca expirará (predefinição).|
|ttl = -1   |O TTL está ativado. O artigo nunca expirará.|
|ttl = 2000 |O TTL está ativado. O artigo expirará após 2000 segundos.|


### <a name="example-3"></a>Exemplo 3

TTL no recipiente é definido para 1000 (DefaultTimeToLive = 1000)

|TTL no item| Resultado|
|---|---|
|ttl = nulo|    O TTL está ativado. O artigo expirará após 1000 segundos (predefinição).|
|ttl = -1   |O TTL está ativado. O artigo nunca expirará.|
|ttl = 2000 |O TTL está ativado. O artigo expirará após 2000 segundos.|

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar o tempo para viver nos seguintes artigos:

* [Como configurar o tempo para viver](how-to-time-to-live.md)