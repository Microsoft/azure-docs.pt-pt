---
title: Expirar dados em Azure Cosmos DB com vida útil
description: Com o TTL, Microsoft Azure Cosmos DB fornece a capacidade de ter documentos limpos automaticamente do sistema após um período de tempo.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: c504e2f574970142942945de5a0a9fb409bb166b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498308"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Vida útil (TTL) no Azure Cosmos DB 

Com **a vida útil ou** TTL, Azure Cosmos DB fornece a capacidade de excluir itens automaticamente de um contêiner após um determinado período de tempo. Por padrão, você pode definir a vida útil no nível de contêiner e substituir o valor por item. Depois de definir o TTL em um contêiner ou em um nível de item, Azure Cosmos DB removerá automaticamente esses itens após o período de tempo, desde a hora em que foram modificados pela última vez. O valor de vida útil é configurado em segundos. Quando você configurar o TTL, o sistema excluirá automaticamente os itens expirados com base no valor TTL, sem a necessidade de uma operação de exclusão emitida explicitamente pelo aplicativo cliente.

A exclusão de itens expirados é uma tarefa em segundo plano que consome [unidades de solicitação](request-units.md)restantes, que são unidades de solicitação que não foram consumidas por solicitações do usuário. Mesmo após o TTL expirar, se o contêiner estiver sobrecarregado com solicitações e se não houver RU suficiente disponível, a exclusão de dados será atrasada. Os dados são excluídos quando há um RUs suficiente disponível para executar a operação de exclusão. Embora a exclusão de dados seja atrasada, os dados não são retornados por nenhuma consulta (por qualquer API) após o tempo de vida expirar.

## <a name="time-to-live-for-containers-and-items"></a>Vida útil para contêineres e itens

O valor de vida útil é definido em segundos e é interpretado como um Delta desde o momento em que um item foi modificado pela última vez. Você pode definir a vida útil em um contêiner ou um item dentro do contêiner:

1. **Vida útil em um contêiner** (definir usando `DefaultTimeToLive`):

   - Se ausente (ou definido como nulo), os itens não expirarão automaticamente.

   - Se presente e o valor for definido como "-1", ele será igual ao infinito e os itens não expirarão por padrão.

   - Se presente e o valor for definido como algum número *"n"* – os itens expirarão em *"n"* segundos após a hora da última modificação.

2. **Vida útil em um item** (definido usando `ttl`):

   - Essa propriedade será aplicável somente se `DefaultTimeToLive` estiver presente e não estiver definida como NULL para o contêiner pai.

   - Se estiver presente, ele substituirá o valor `DefaultTimeToLive` do contêiner pai.

## <a name="time-to-live-configurations"></a>Configurações de vida útil

* Se TTL for definido como *"n"* em um contêiner, os itens nesse contêiner expirarão após *n* segundos.  Se houver itens no mesmo contêiner que tenham sua própria vida útil, defina como-1 (indicando que eles não expiram) ou se alguns itens tiverem substituído a configuração de vida útil com um número diferente, esses itens expirarão com base em seu próprio valor de TTL configurado. 

* Se TTL não estiver definido em um contêiner, a vida útil em um item nesse contêiner não terá nenhum efeito. 

* Se TTL em um contêiner for definido como-1, um item nesse contêiner que tem a vida útil definida como n expirará após n segundos, e os itens restantes não expirarão. 

A exclusão de itens com base em TTL é gratuita. Não há custo adicional (ou seja, nenhum RUs adicional é consumido) quando o item é excluído como resultado da expiração TTL.

## <a name="examples"></a>Exemplos

Esta seção mostra alguns exemplos com valores de vida útil diferentes atribuídos a contêineres e itens:

### <a name="example-1"></a>Exemplo 1

TTL no contêiner é definido como nulo (DefaultTimeToLive = NULL)

|TTL no item| Resultado|
|---|---|
|TTL = nulo|    O TTL está desabilitado. O item nunca expirará (padrão).|
|TTL =-1   |O TTL está desabilitado. O item nunca expirará.|
|TTL = 2000 |O TTL está desabilitado. O item nunca expirará.|


### <a name="example-2"></a>Exemplo 2

TTL no contêiner é definido como-1 (DefaultTimeToLive =-1)

|TTL no item| Resultado|
|---|---|
|TTL = nulo |O TTL está habilitado. O item nunca expirará (padrão).|
|TTL =-1   |O TTL está habilitado. O item nunca expirará.|
|TTL = 2000 |O TTL está habilitado. O item expirará após 2000 segundos.|


### <a name="example-3"></a>Exemplo 3

TTL no contêiner é definido como 1000 (DefaultTimeToLive = 1000)

|TTL no item| Resultado|
|---|---|
|TTL = nulo|    O TTL está habilitado. O item expirará após 1000 segundos (padrão).|
|TTL =-1   |O TTL está habilitado. O item nunca expirará.|
|TTL = 2000 |O TTL está habilitado. O item expirará após 2000 segundos.|

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar a vida útil nos seguintes artigos:

* [Como configurar a vida útil](how-to-time-to-live.md)
