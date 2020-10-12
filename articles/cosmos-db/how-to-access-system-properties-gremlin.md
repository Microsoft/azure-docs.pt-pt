---
title: Aceder propriedades de documento do sistema via Azure Cosmos DB Graph
description: Saiba como ler e escrever propriedades de documentos do sistema Cosmos DB através da API gremlin
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/10/2019
author: SnehaGunda
ms.author: sngun
ms.openlocfilehash: c03e4db30d590df21a8ceb3c483ece4b59e548d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397322"
---
# <a name="system-document-properties"></a>Propriedades de documento do sistema

A Azure Cosmos DB tem [propriedades do sistema](/rest/api/cosmos-db/databases) tais ```_ts``` ```_self``` como, , , e ```_attachments``` em todos os ```_rid``` ```_etag``` documentos. Além disso, o motor do Gremlin adiciona as propriedades ```inVPartition``` e ```outVPartition``` nas margens. Por padrão, estas propriedades estão disponíveis para traversal. No entanto, é possível incluir propriedades específicas, ou todas elas, em Gremlin traversal.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

Esta propriedade é utilizada para controlo da simultaneidade otimista. Se a aplicação precisar de quebrar o funcionamento em algumas travessias separadas, pode usar a propriedade eTag para evitar a perda de dados em escritas simultâneas.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>TTL

Se a recolha tiver expiração de documento ativada e os documentos tiverem ```ttl``` propriedade definida sobre eles, então esta propriedade estará disponível em Gremlin traversal como um vértice regular ou propriedade de borda. ```ProjectionStrategy``` não é necessário para permitir a exposição de propriedades de tempo para viver.

O vértice criado com o percurso abaixo será eliminado automaticamente em **123 segundos**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Passos seguintes
* [Simultaneidade Otimista do Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Tempo para Viver (TTL)](time-to-live.md) em Azure Cosmos DB
