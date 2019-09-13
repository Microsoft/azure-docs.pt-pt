---
title: Acessar propriedades do documento do sistema via grafo de Azure Cosmos DB
description: Saiba como ler e gravar Cosmos DB Propriedades do documento do sistema via API do Gremlin
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: 4354d37a32bde006a9bee70c39df1fee9b269365
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910662"
---
# <a name="system-document-properties"></a>Propriedades do documento do sistema

Azure Cosmos DB tem [Propriedades do sistema](https://docs.microsoft.com/rest/api/cosmos-db/databases) , ```_ts```como ```_self```, ```_attachments``` ```_rid```,, e ```_etag``` em cada documento. Além disso, o motor do Gremlin adiciona as propriedades ```inVPartition``` e ```outVPartition``` nas margens. Por padrão, essas propriedades estão disponíveis para passagem. No entanto, é possível incluir propriedades específicas ou todas elas em Gremlin Traversal.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>Marca E

Esta propriedade é utilizada para controlo da simultaneidade otimista. Se o aplicativo precisar interromper a operação em alguns atravessamentos separados, ele poderá usar a propriedade eTag para evitar a perda de dados em gravações simultâneas.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>TTL

Se a coleção tiver a expiração de documento ```ttl``` habilitada e os documentos tiverem a propriedade definida nelas, essa propriedade estará disponível em passagem Gremlin como uma propriedade de vértice ou de borda regular. ```ProjectionStrategy```Não é necessário para habilitar a exposição da propriedade de vida útil.

O vértice criado com o percurso abaixo será eliminado automaticamente em **123 segundos**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Passos Seguintes
* [Simultaneidade Otimista do Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Vida útil (TTL)](time-to-live.md) no Azure Cosmos DB
