---
title: incluir ficheiro
description: incluir ficheiro
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f7495c52e36bdc207145f17ec72eb57b7ebf1784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279281"
---
Os enlaces do Cosmos DB do Azure só são suportados para utilização com a API de SQL. Para todas as outras APIs DB Da Azure Cosmos, deve aceder à base de dados a partir da sua função utilizando o cliente estático para a sua API, incluindo a [API da Azure Cosmos DB para MongoDB,](../articles/cosmos-db/mongodb-introduction.md) [Cassandra API,](../articles/cosmos-db/cassandra-introduction.md) [Gremlin API](../articles/cosmos-db/graph-introduction.md)e [API de Mesa.](../articles/cosmos-db/table-introduction.md)
