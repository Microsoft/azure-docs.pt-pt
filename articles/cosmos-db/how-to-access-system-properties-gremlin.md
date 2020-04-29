---
title: Propriedades de documentos do sistema de acesso via Azure Cosmos DB Graph
description: Saiba como ler e escrever propriedades de documentos do sistema Cosmos DB via Gremlin API
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 4ed7e67ae0ef027b260d0e0f0407e4e05ed5a8f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898310"
---
# <a name="system-document-properties"></a>Propriedades de documentos do sistema

A Azure Cosmos DB ```_ts```tem ```_self``` ```_attachments```propriedades ```_rid```do ```_etag``` [sistema](https://docs.microsoft.com/rest/api/cosmos-db/databases) como, e em todos os documentos. Além disso, o motor do Gremlin adiciona as propriedades ```inVPartition``` e ```outVPartition``` nas margens. Por padrão, estas propriedades estão disponíveis para traversal. No entanto, é possível incluir propriedades específicas, ou todas elas, em Gremlin traversal.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

Esta propriedade é utilizada para controlo da simultaneidade otimista. Se a aplicação precisar de quebrar a operação em alguns traversals separados, pode usar propriedade eTag para evitar a perda de dados em escritos simultâneos.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>TTL

Se a recolha tiver expiração ```ttl``` de documento satisfeituto e os documentos tiverem propriedades definidas sobre eles, então esta propriedade estará disponível em Gremlin traversal como um vértice regular ou propriedade de borda. ```ProjectionStrategy```não é necessário para permitir a exposição de propriedades de tempo para viver.

O vértice criado com o percurso abaixo será eliminado automaticamente em **123 segundos**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Passos seguintes
* [Simultaneidade Otimista do Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Tempo para viver (TTL)](time-to-live.md) em Azure Cosmos DB
