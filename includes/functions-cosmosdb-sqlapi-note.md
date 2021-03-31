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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "76279281"
---
Os enlaces do Cosmos DB do Azure só são suportados para utilização com a API de SQL. Para todas as outras APIs Azure Cosmos DB, você deve aceder à base de dados a partir da sua função usando o cliente estático para a sua API, incluindo [API da Azure Cosmos DB para MongoDB,](../articles/cosmos-db/mongodb-introduction.md) [Cassandra API,](../articles/cosmos-db/cassandra-introduction.md) [Gremlin API,](../articles/cosmos-db/graph-introduction.md)e [Tabela API.](../articles/cosmos-db/table-introduction.md)
