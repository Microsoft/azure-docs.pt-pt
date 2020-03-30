---
title: Expirar dados em Azure Cosmos DB com tempo para viver
description: Com a TTL, o Microsoft Azure Cosmos DB fornece a capacidade de ter documentos automaticamente purgados do sistema após um período de tempo.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188725"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Time to Live [TTL] no Azure Cosmos DB 

Com **time to Live** ou TTL, o Azure Cosmos DB oferece a capacidade de apagar automaticamente itens de um recipiente após um determinado período de tempo. Por padrão, pode definir o tempo para viver ao nível do contentor e sobrepor o valor numa base por item. Depois de colocar o TTL num recipiente ou a um nível de item, o Azure Cosmos DB removerá automaticamente estes itens após o período de tempo, desde a última vez que foram modificados. O tempo para viver valor é configurado em segundos. Quando configurar o TTL, o sistema eliminará automaticamente os itens expirados com base no valor TTL, sem precisar de uma operação de eliminação que seja explicitamente emitida pela aplicação do cliente.

A eliminação de itens expirados é uma tarefa de fundo que consome [unidades](request-units.md)de pedido restantes, ou seja, Unidades de Pedido que não foram consumidas por pedidos do utilizador. Mesmo depois de expirado o TTL, se o recipiente estiver sobrecarregado com pedidos e se não houver RU suficiente disponível, a eliminação de dados é adiada. Os dados são eliminados uma vez que existam RUs suficientes disponíveis para executar a operação de eliminação. Embora a eliminação de dados seja adiada, os dados não são devolvidos por quaisquer consultas (por qualquer API) após a expiração do TTL.

## <a name="time-to-live-for-containers-and-items"></a>Hora de viver para recipientes e itens

O tempo para viver valor é definido em segundos, e é interpretado como um delta desde que um item foi modificado pela última vez. Pode definir tempo para viver num recipiente ou num item dentro do recipiente:

1. **Hora de viver num** recipiente `DefaultTimeToLive`(definido usando):

   - Se faltar (ou definido para nulo), os itens não expiram automaticamente.

   - Se presente e o valor for definido para "-1", é igual ao infinito, e os itens não expiram por defeito.

   - Se presente e o valor for definido para algum número *"n"* – os itens expirarão *"n"* segundos após o seu último tempo modificado.

2. **Hora de viver num item** (conjunto usando): `ttl`

   - Este Imóvel só `DefaultTimeToLive` é aplicável se estiver presente e não for definido para o recipiente-mãe.

   - Se presente, sobrepõe-se ao `DefaultTimeToLive` valor do recipiente-mãe.

## <a name="time-to-live-configurations"></a>Configurações de tempo para viver

* Se a TTL estiver definida para *"n"* num recipiente, os itens desse recipiente expirarão após *n* segundos.  Se houver itens no mesmo recipiente que tenham o seu próprio tempo de vida, definidos para -1 (indicando que não expiram) ou se alguns itens tiverem ultrapassado o tempo de viver com um número diferente, estes itens expiram com base no seu próprio valor TTL configurado. 

* Se a TTL não estiver colocada num recipiente, então o tempo de viver num item neste recipiente não tem qualquer efeito. 

* Se o TTL num recipiente estiver definido para -1, um item neste recipiente que tenha tempo para viver definido para n, expirará após n segundos, e os restantes itens não expirarão.

## <a name="examples"></a>Exemplos

Esta secção mostra alguns exemplos com tempo sinuoso para viver valores atribuídos a contentores e itens:

### <a name="example-1"></a>Exemplo 1

TTL no recipiente está definido para nulo (DefaultTimeToLive = nulo)

|TTL no item| Resultado|
|---|---|
|ttl = nulo|    A TTL está desativada. O item nunca expirará (predefinido).|
|ttl = -1   |A TTL está desativada. O artigo nunca expirará.|
|ttl = 2000 |A TTL está desativada. O artigo nunca expirará.|


### <a name="example-2"></a>Exemplo 2

TTL no recipiente está definido para -1 (DefaultTimeToLive = -1)

|TTL no item| Resultado|
|---|---|
|ttl = nulo |A TTL está ativada. O item nunca expirará (predefinido).|
|ttl = -1   |A TTL está ativada. O artigo nunca expirará.|
|ttl = 2000 |A TTL está ativada. O artigo expirará após 2000 segundos.|


### <a name="example-3"></a>Exemplo 3

TTL no recipiente está definido para 1000 (DefaultTimeToLive = 1000)

|TTL no item| Resultado|
|---|---|
|ttl = nulo|    A TTL está ativada. O artigo expirará após 1000 segundos (predefinido).|
|ttl = -1   |A TTL está ativada. O artigo nunca expirará.|
|ttl = 2000 |A TTL está ativada. O artigo expirará após 2000 segundos.|

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar o Tempo para Viver nos seguintes artigos:

* [Como configurar o Tempo para Viver](how-to-time-to-live.md)
