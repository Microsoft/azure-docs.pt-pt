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
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279281"
---
Os enlaces do Cosmos DB do Azure só são suportados para utilização com a API de SQL. Para todas as outras APIs de Azure Cosmos DB, você deve acessar o banco de dados de sua função usando o cliente estático para sua API, incluindo a [API do Azure Cosmos DB para MongoDB](../articles/cosmos-db/mongodb-introduction.md), [API do Cassandra](../articles/cosmos-db/cassandra-introduction.md), [API Gremlin](../articles/cosmos-db/graph-introduction.md)e [API de tabela](../articles/cosmos-db/table-introduction.md).
