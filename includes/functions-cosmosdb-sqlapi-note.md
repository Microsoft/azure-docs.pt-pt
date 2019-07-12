---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c68b56463625a09f9c8341881284706ab8250f46
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608332"
---
Os enlaces do Cosmos DB do Azure só são suportados para utilização com a API de SQL. Para todas as outras APIs do Azure Cosmos DB, deve acessar o banco de dados da sua função com o cliente de estático para a sua API, incluindo [API do Azure Cosmos DB para o MongoDB](../articles/cosmos-db/mongodb-introduction.md), [Cassandra API](../articles/cosmos-db/cassandra-introduction.md), [ API do gremlin](../articles/cosmos-db/graph-introduction.md), e [API de tabela](../articles/cosmos-db/table-introduction.md).
